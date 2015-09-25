# neo2vim
aims at translating neovim python plugins into vim plugins
# install
    
    gem install neo2vim

([rubygems.org](https://rubygems.org/gems/neo2vim) web page of the gem)

# usage

    neo2vim /path/to/input/plugin.py /path/to/output_dir

# File structure

`NAME` is extracted from neovim plugin class name.

* `plugin/{NAME}.vim`: Definitions of autocommands, commands, and functions
* `autoload/{NAME}.vim`: VimScript-Python bridge
* `autoload/{NAME}.vim.py`: Plugin implementation

# Call plugin functions from vimscript

Every public methods in plugin class is exposed via `{plugin_id}#{method_name}` function in vim.
Where `plugin_id` is `lower_camel_case` of plugin class name.

Plugin class should define the function for handling RPC:
```python
  @neovim.rpc_export('{plugin_id}_invoke', sync = True)
  def _invoke(self, name, args):
    return getattr(self, name)(*args)
```

## Example

```python
# rplugin/python/my_plugin.py
@neovim.plugin
class MyPlugin:
  def __init__(self, vim):
    self.vim = vim

  @neovim.rpc_export('my_plugin_invoke', sync = True)
  def _invoke(self, name, args):
    return getattr(self, name)(*args)

  def hello(self):
    self.vim.eval('echo Hello!')
```

```vim
" In vim
:call my_plugin#hello()
```
