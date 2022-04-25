Deprecations
============

## Deprecating a module

## Deprecating a parameter

Add the keywords `removed_in_version` and `removed_from_collection` to the parameter. Example:

```python
dist=dict(type='str', removed_in_version='4.0.0', removed_from_collection='community.general'),
```

## Deprecating a parameter alias

Add the key word `deprecated_aliases` to the parameter. Example:

```python
update_cache=dict(
    default="no", aliases=["update-cache"], type='bool',
    deprecated_aliases=[dict(name='update-cache', version='5.0.0', collection_name='community.general')],
),
```

## Deprecating a parameter default value

There is no embedded support for deprecating a default value in Ansible. This is best achieved by following the steps:

* Remove the default from the parameter
* Remove the `default: old_default` from the documentation, replacing it with a line in the description describing the current default value and the deprecation, as in: 
  ```yaml
  - The default value for this param is C(old_default) but that is being deprecated 
  and it will be replaced with C(new_default) in community.general 8.0.0.
  ```
* Then add a code like:
  ```python
  if module.params['param1'] is None:
      param1 = old_default
      module.deprecate(
          'The default value {0} is being deprecated and it will be replaced by {1}'.format(
              old_default, new_default
          ),
          version='8.0.0', 
          collection_name='community.general'
      )
  ```

## Deprecating a parameter choice value

Similarly to the deprecation of the default value above, there is no embedded mechanism to deprecate one specific value from a parameter's `choices` list. The best way to achieve it is by following the steps:

* Add a code:
  ```python
  if module.params['param1'] == deprecated_choice:
      module.deprecate(
          'The value {0} is being deprecated'.format(deprecated_choice), 
          version='8.0.0', 
          collection_name='community.general'
      )
  ```

## Deprecating a behaviour

Deprecating a behaviour can be slightly trickier because many times the change in the behaviour is not directly linked to a parameter or its value. That means that the module user will have no mechanism to acknowledge the deprecation of the old behaviour and choose the new one. 

### Removing a behaviour triggered by module parameter or parameter value

If the old behaviour were tied to some parameter, we could deprecate that parameter or its value and that behaviour would not be triggered again, quite similarly to the strategy of the previous section (Deprecating a parameter choice value).

Say an `int` parameter `x` used to accept any value in the range 0-500, but the behaviour of the module when the value is in the 400-500 range will no longer be accepted. We could deprecate it with:

```python
if module.params['x'] >= 400:
    module.deprecate(
        'Parameter "x" values greater than or equal to 400 are deprecated', 
        version='8.0.0', 
        collection_name='community.general'
    )
```

### Changing a behaviour triggered by module parameter or parameter value

When the behaviour is being deprecated but the parameter values that triggered will continue to be valid, but will cause different actions in the module, then we need to provide the user with a switch to control the module behaviour and the generation of the deprecation warning.

Let's make a small variation on the example above and say that now, when `x >= 400` the behaviour should be the same as with `x >= 300`. In that case, it would be necessary to create a "behaviour switch" parameter. The steps would be like:

* Add a `bool` parameter, giving a meaningful name for the situation and **ensuring the default value indicates the old behaviour**. In this case, let's say `old_behaviour_when_x_gt_400`:

  ```python
  argument_specs=dict(
      # ...
      old_behaviour_when_x_gt_400=dict(type='bool', default=True),
  )
  ```
  Don't forget to add that parameter and its description to the documentaion block of the module.
