Command Runner
==============

The Command Runner is a wrapper around the standard `AnsibleModule.run_command()` method.
It provides sensible defaults and a consistent argument formatting mechanism.
It makes command execution code easier to read and understand.
It provides a consistent mechanism to process the outcome of the command execution.

## Quickstart

This example below is adapted from the actual code for `community.general.xfconf`.

```python
from ansible.module_utils.basic import AnsibleModule
from ansible.module_utils.parsing.convert_bool import boolean
from ansible_collections.community.general.plugins.module_utils.cmd_runner import CmdRunner, cmd_runner_fmt as fmt


@fmt.unpack_args
def _values_fmt(values, value_types):
    result = []
    for value, value_type in zip(values, value_types):
        if value_type == 'bool':
            value = 'true' if boolean(value) else 'false'
        result.extend(['--type', '{0}'.format(value_type), '--set', '{0}'.format(value)])
    return result


class MyModule(object):
    def __init__(self, module):
        self.module = module

        # create a runner object
        self.runner = CmdRunner(
            module,
            command='xfconf-query',
            arg_formats=dict(
                channel=fmt.as_opt_val("--channel"),
                property=fmt.as_opt_val("--property"),
                force_array=fmt.as_bool("--force-array"),
                reset=fmt.as_bool("--reset"),
                create=fmt.as_bool("--create"),
                list_arg=fmt.as_bool("--list"),
                values_and_types=fmt.as_func(_values_fmt),
            ),
        )

    def do_something(self):
        # create a runner context, specify the argument order and other options for the context
        with self.runner.context("list_arg create force_array channel property values_and_types") as ctx:
            result = ctx.run(channel="xfwm4", property="/general/inactive_opacity", list_arg=True)
            # /usr/bin/xfconf-query --list --channel xfwm4 --property /general/inactive_opacity

            result = ctx.run(list_arg=True, **self.module.params)
            # assuming that module has params channel and property and their values are the same as last example:
            # /usr/bin/xfconf-query --list --channel xfwm4 --property /general/inactive_opacity

            result = ctx.run(create=True, channel="xfwm4", property="/general/workspace_names",
                             values_and_types=[("WS1", "WS2"), ("string", "string")])
            # /usr/bin/xfconf-query --create --channel xfwm4 --property /general/workspace_names \
            #   --type string --set WS1 --type string --set WS2
```

## API


## Formats

## Processing Output

## Localization

## Check Mode
