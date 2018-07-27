Each script should be configured in conf/runners/_conf-name_.json file, where _conf-name_ is an unique name for each script. Each configuration file should contain only one script. Configuration should be a valid JSON file.

Examples of configuration files can be found in [master/testing/configs](../blob/master/samples/configs) or see a full config at the bottom of the page.

Configurable properties:
* [name](#name)
* [script_path](#script_path)
* [description](#description)
* [requires_terminal](#requires_terminal)
* [working_directory](#working_directory)
* [bash_formatting](#bash_formatting)
* [output_files](#output_files)
* [parameters](#parameters)
  * [parameters](#parameters)

---

### `name`
User-friendly script name. Will be displayed to user  

_Required_: no  
_Type_: string  
_Default_: the filename without extension  
_Example_: `"name": "My example script"`  

### `script_path`
Path to the script (relative to working directory) OR just a command.  

_Required_: **yes**  
_Type_: string  
_Windows important_: any non-batch scripts should be run as a command, e.g. 'python my_script.py'  
_Example_: `"script_path": "/some/path/to/script.sh"`  
_Example2_: `"script_path": "python my_script.py"`  

### `description`
User-friendly script description, which will be shown to a user  

_Required_: no  
_Type_: string  
_Example_: `"description": "This script shuts down the server"`  

### `requires_terminal`
(Linux only)  
Specifies, if the script should be run in pseudo terminal mode. This is useful because some programs behave differently in terminal and non-interactive modes.  

_Required_: no  
_Type_: boolean  
_Default_: true  

### `working_directory`
Working directory for the script  

_Required_: no  
_Type_: string  
_Default_: script-server directory  

### `bash_formatting`
Enables ANSI escape command parsing and showing formatted output on the user interface. E.g. \033[01;31m is a bold red text  
Supported escape sequences: 16 text colors and 16 background colors, 4 text styles  

_Required_: no  
_Type_: boolean  
_Default_: true for linux and mac, false otherwise  

### `output_files`
List of files, which will be downloadable by user after a script execution, can be:  
- file path, with:  
  - \* and ** wildcard support  
  - substitution of script parameters with $$$parameter_name (e.g. /home/me/$$$f1.txt, when f1=readme, becomes /home/me/readme.txt)
- regex pattern, for searching file path in script output:
  - should be surrounded with #
  - matching group number can be specified with number# (e.g. #2#)
  - #any_path can be used for searching paths in the output
  - if the path cannot be found, it will be ignored
  - secure parameter values are protected with a mask during the search  

All the matching files are copied locally and stored for at least 24 hours. Only the same user can access the files.  
  
_Required_: no  
_Type_: array  
_Example_: 
```javascript
"output_files": [
	/* Returns the file under the path */
	"/var/log/server.log",
	
	/* Resolves param1 'value' and recursively searches for 'value'.dat in /tmp/results */
	"/tmp/results/**/$$$param1.dat",
	
	/* Searches for any paths in in the script output, and then substitutes each occurence in the path */
	"~/##any_path#/file.txt",
	
	/* Searches for any 8 digits in the script output and builds /var/log/my_8digits.log paths */
	"/var/log/my_#[0-9]{8}#.log",
	
	/* Searches for username='value' in the script output and builds /home/'value'/file.txt paths */
	"/home/#1#username=(\w+)#/file.txt"
]
```  

### `parameters`
List of script parameters. Parameters are shown on the GUI and passed to a script exactly in the same order.
Below are the all allowed parameter properties  

_Required_: no  
_Type_: array of dicts  
_Example_:
```
"parameters": [
	{
		"name": "param1",
		"param": "-a"
	},
	{
		"name": "param2",
		"param": "-b"
	}
]
```  

### &nbsp;&nbsp;&nbsp;- `name`
The name of the parameter, which will be shown to the user.  
Required for non-constant parameters  

_Required_: yes (for non-constant parameters)  
_Type_: string  


### &nbsp;&nbsp;&nbsp;- `param`
Can be used for specifying flag for the parameter (e.g. -p in `script.sh -p myval`). If omitted, parameter will be position based  

_Required_: no  
_Type_: string  


### &nbsp;&nbsp;&nbsp;- `no_value`
Allows to pass only a flag to the script, without any value.
`param` property is required in this case.
This infers parameter `type` as boolean (the `type` property should be ommitted)  

_Required_: no  
_Type_: boolean  
_Default_: false  


### &nbsp;&nbsp;&nbsp;- `description`
User-friendly description of the parameter, shown to the user  

_Required_: no  
_Type_: string  


### &nbsp;&nbsp;&nbsp;- `required`
Marks parameter as required for the user  

_Required_: no  
_Type_: boolean  
_Default_: false  


### &nbsp;&nbsp;&nbsp;- `constant`
Don't show the parameter to a user, but pass it to a script with the value of `default` field  

_Required_: no  
_Type_: boolean  
_Default_: false  


### &nbsp;&nbsp;&nbsp;- `default`
Default value shown to a user  
Environment variables can be specified with $$ (e.g. `"default": "$$HOME"`)  

_Required_: no (for constants yes)  
_Type_: depends on the parameter type  


### &nbsp;&nbsp;&nbsp;- `type`
Parameter type, which affects how a parameter is shown to a user (checkbox, text field, select box, etc.) and how it's passed to a script (for example as a single argument or as multiple arguments)  
If `no_value` is set to true, then the `type` is ignored and always is boolean  
For detail description of each type and it's supported properties see the section after properties description.  

_Allowed values_: int, list, text, file_upload  
_Required_: no  
_Type_: string  
_Default_: "text"  


### &nbsp;&nbsp;&nbsp;- `max`
Maximal allowed value  

_Required_: no  
_Supported types_: int  
_Type_: string  


### &nbsp;&nbsp;&nbsp;- `min`
Mininal allowed value  

_Required_: no  
_Supported types_: int  
_Type_: string  


### &nbsp;&nbsp;&nbsp;- `secure`
Don't show value on server anywhere, replace it with a mask  

_Required_: no  
_Type_: boolean  
_Default_: false  


### &nbsp;&nbsp;&nbsp;- `values`
List of allowed values for the parameter. Can be either predefined values or a result from another script invocation  

_Required_: yes (for list)  
_Supported types_: list  
_Type_: array or object  
_Example1_: `"values": [ "Apple", "Orange", "Banana" ]`  
_Example2_: `"values": { "script": "ls /home/me/projects" }`  

---  

## Parameter types
### text
Simple text field. The value is passed to a script as it is.  
If parameter is `secure`, text field on UI will be masked.  
_Default value type_: string  

### int
The same as text, but performs number validation.  
Can be constrained with `min` and `max` properties  
_Default value type_: string or integer  

### boolean (no_value)
Parameter is shown to a user as a checkbox. 
Since this is supported only for `no_value` parameters (flags only), value is not passed to a script  
_Default value type_: boolean  

### list
Provides to user a list with allowed values (as a combobox).  
Allowed values should be specified with `values` property  
_Default value type_: string  

### file_upload
Allows user to upload a file to the server as a parameter.  
The file is uploaded to the server and stored in a temp folder. Then the file is passed to a script as an absolute path  
`secure` flag has no effect for this type  
_Default value type_: unsupported  

---  

## Example

```javascript
{
  "name": "My example script",
  "script_path": "/some/path/to/script.sh",
  /** or "script_path": "python my_script.py" */
  "description": "Some useful description on what the script does",
  "requires_terminal": true,
  "working_directory": "/home/me/temp",
  "bash_formatting": true,
  "output_files": [
    "##any_path/localhost.*.log",
    "~/simple.txt"
  ],
  "parameters": [
    {
      "name": "MyParam",
      "param": "-p",
      "no_value": false,
      "description": "This parameter is used for filename",
      "required": false,
      "default": "empty",
      "type": "int",
      "constant": false,
      "max": "50",
      "min": "-1",
      "secure": true,
      "values": [
        "Apple",
        "Orange",
        "Banana"
      ]
      /** or "values": { "script": "ls /home/me/projects" } */
    }
  ]
}
```
