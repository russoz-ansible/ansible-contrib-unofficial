ModuleHelper Classes
====================

## ModuleHelper

`ModuleHelper` is the base class providing features to modules.

### Quickstart

```python
from ansible_collections.community.general.plugins.module_utils.module_helper import ModuleHelper

class MyModule(ModuleHelper):
    output_params = ['name']   # parameters listed here will be included in the module output
    module = dict(
        argument_spec=dict(
            name=dict(type='str'),
            include_deps=dict(type='bool', default=False),
            include_injected=dict(type='bool', default=False),
        ),
        supports_check_mode=True,
    )

    def __init_module__(self):
        # module set up

        # automatically tracks if this variable is changed and produce diff
        self.vars.set('application', self._retrieve_installed(), change=True, diff=True)

    def __run__(self):
        # do something

        if something_goes_wrong:
            self.do_raise("Cannot complete task")

    def __quit_module__(self):
        # module clean up or final tasks

        self.vars.set('application', self._retrieve_installed())


def main():
    MyModule.execute()


if __name__ == '__main__':
    main()
```

### Features

`ModuleHelper` will provide some perks to your module class:
* Automatic handling of Exceptions:
  Use `self.do_raise()` to indicate problems, `module.fail_json()` will be called accordingly
* Automatic variables:
  Use `self.vars` to better manage the module's state and parameters. Allows:
  * Include the variable in the module output
  * Make the variable a fact
  * Track changes in the variable value and set `changed` accordingly in the module output
  * Include variable in module's diff output
  See more in the [VarDict](var-dict.md) documentation.
* Other features described in more details in their own documentation.

### Examples

* [community.general.xfconf_info](https://github.com/ansible-collections/community.general/blob/main/plugins/modules/system/xfconf_info.py)

## StateModuleHelper

Many modules control their actions using a `state` parameter. ModuleHelper provides the class
`StateModuleHelper` to facilitate implementing a state-driven module.

### Quickstart

```python
from ansible_collections.community.general.plugins.module_utils.module_helper import StateModuleHelper


class Blacklist(StateModuleHelper):
    output_params = ('name', 'state')
    module = dict(
        argument_spec=dict(
            name=dict(type='str', required=True),
            state=dict(type='str', default='present', choices=['absent', 'present']),
            blacklist_file=dict(type='str', default='/etc/modprobe.d/blacklist-ansible.conf'),
        ),
        supports_check_mode=True,
    )

    def state_absent(self):
        if not self.vars.is_blacklisted:
            return
        self.vars.is_blacklisted = False
        self.vars.lines = [line for line in self.vars.lines if not self.pattern.match(line.strip())]

    def state_present(self):
        if self.vars.is_blacklisted:
            return
        self.vars.is_blacklisted = True
        self.vars.lines = self.vars.lines + ['blacklist %s' % self.vars.name]


def main():
    Blacklist.execute()


if __name__ == '__main__':
    main()
```

(Trimmed down version of the `community.general.blacklist` module)

### Features

When using `StateModuleHelper`, the module developer must implement methods named
`state_VALUE()` for each `VALUE` that the `state` parameter can take.

If the deciding parameter has a different name, set the parameter name in the
class variable `state_param`, as in:

```python
class MyStateModule(StateModuleHelper):
    module = dict(
        argument_spec=dict(
            operation=dict(type='str', choices=['create', 'edit']),
        )
    )
    state_param = "operation"

    def operation_create(self):
        # ...

    def operation_edit(self):
        # ...
```

### Examples

* [community.general.jira](https://github.com/ansible-collections/community.general/blob/main/plugins/modules/web_infrastructure/jira.py)

## CmdModuleHelper & CmdStateModuleHelper

The original idea of `CmdModuleHelper` and its sibling `CmdStateModuleHelper`
was to provide an easier, streamlined mechanism to deal with external commands.

Over time, it was observed that having specialized subclasses for that created
an artificial coupling between that mechanism and `ModuleHelper`, so these
two classes are no longer recommended (they should be deprecated at some point
in the future), in favour of using [CmdRunner](cmd-runner.md).
