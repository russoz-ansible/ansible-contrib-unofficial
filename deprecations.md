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
