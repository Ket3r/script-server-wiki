Each script should be configured in conf/runners/_conf-name_.json file, where _conf-name_ is a unique name for each script. Each configuration file should contain only one script. Configuration should be a valid JSON file.  

Also, you can configure scripts using /admin.html page on your server.  

Examples of configuration files can be found in [[stable/testing/configs|https://github.com/bugy/script-server/tree/stable/samples/configs]].

Configurable properties:
* [name](#name)
* [script_path](#script_path)
* [description](#description)
* [working_directory](#working_directory)
* [group](#group)
* [allowed_users](#allowed_users)
* [output_files](#output_files)
* [requires_terminal](#requires_terminal)
* [bash_formatting](#bash_formatting)
* [include](#include)
* [hidden](#hidden)
* [parameters](#parameters)
  * [name](#--name)
  * [param](#--param)
  * [no_value](#--no_value)
  * [description](#--description)
  * [required](#--required)
  * [constant](#--constant)
  * [default](#--default)
  * [type](#--type)
  * [secure](#--secure)
  * [env_var](#--env_var)
  * \+ additional fields for [different types](#parameter-types)
  

Parameter types:
* [text](#text)
* [int](#int)
* [boolean/no_value](#boolean-no_value)
* [list](#list)
* [multiselect](#multiselect)
* [file_upload](#file_upload)
* [server_file](#server_file)
* [ip](#ip)


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
Markdown with GitHub flavour can be used  

_Required_: no  
_Type_: string  
_Example_: `"description": "This script shuts down the server"`  


### `working_directory`
Working directory for the script  

_Required_: no  
_Type_: string  
_Default_: script-server directory  



### `group`
Allows to aggregate multiple scripts under the same collapsible header on the UI. Just specify the same group for those scripts  

_Required_: no  
_Type_: string  

### `allowed_users`
List of users, who can access the script  

_Required_: no  
_Type_: json array  
_Default_: any user  

### `output_files`
List of files, which will be downloadable by user after a script execution.
This option supports the following features:  
  - \* and ** wildcard
  - substitution of script parameters with ${parameter_name}
  - use regex pattern for searching file path in a script output:
    - should be surrounded with #
    - matching group number can be specified with number# (e.g. #2#)
    - \#any_path can be used as an alternative to ** wildcard
    
If a path cannot be found, it will be ignored
Secure parameter values are protected with a mask during the search  

All the matching files are copied locally and stored for at least 24 hours. Only the same user can access the files.  
  
_Required_: no  
_Type_: array  
_Example_: 
```javascript
"output_files": [
	/* Returns the file under the path */
	"/var/log/server.log",
	
	/* Resolves param1 'value' and recursively searches for 'value'.dat in /tmp/results */
	"/tmp/results/**/${param1}.dat",
	
	/* Searches for any paths in in the script output, and then substitutes each occurence in the path */
	"~/##any_path#/file.txt",
	
	/* Searches for any 8 digits in the script output and builds /var/log/my_8digits.log paths */
	"/var/log/my_#[0-9]{8}#.log",
	
	/* Searches for username='value' in the script output and builds /home/'value'/file.txt paths */
	"/home/#1#username=(\w+)#/file.txt"
]
```  

### `requires_terminal`
(Linux only)  
Specifies, if the script should be run in pseudo terminal mode. This is useful because some programs behave differently in terminal and non-interactive modes.  

_Required_: no  
_Type_: boolean  
_Default_: true  


### `bash_formatting`
Enables ANSI escape command parsing and showing formatted output on the user interface. E.g. \033[01;31m is a bold red text  
Supported escape sequences: 16 text colors and 16 background colors, 4 text styles  

_Required_: no  
_Type_: boolean  
_Default_: true for linux and mac, false otherwise  

### `include`
Allows to load configuration from another file.  
Can be useful when multiple scripts are using the same configuration section, then the common part could be extracted to a separate config and then included.  
Usually included files should be `hidden`
If a field exist in main and an included configuration, the main configuration takes precedence.

Include can be dynamic, depending on parameter values (what user specifies). In this case the `include` path should contain ${parameter_name} templates.  
At the moment dynamic include affects only parameter configuration section (new parameters can be added on the fly)     
 
_Required_: no  
_Type_: string (path)


### `hidden`
Hides the script from users  
Can be used to disable a script or for `include`d  configurations

_Required_: no  
_Type_: boolean
_Default_: false

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
Allows to pass only a flag to the script, without any value (e.g. `my_script.sh --verbose`)  
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
User credentials can be used with the following templates `${auth.username}` (authenticated username) or `${auth.audit_name}` (user info, when auth is disabled)

_Required_: no (for constants yes)  
_Type_: depends on the parameter type  


### &nbsp;&nbsp;&nbsp;- `type`
Parameter type, which affects how a parameter is shown to a user (checkbox, text field, select box, etc.) and how it's passed to a script (for example as a single argument or as multiple arguments)  
If `no_value` is set to true, then the `type` is ignored and always is boolean  
For detail description of each type and its supported properties see the section after properties description.  

_Allowed values_: int, list, text, file_upload, multiselect, server_file, ip, ip4, ip6  
_Required_: no  
_Type_: string  
_Default_: "text"  


### &nbsp;&nbsp;&nbsp;- `secure`
Don't show value on server anywhere, replace it with a mask  

_Required_: no  
_Type_: boolean  
_Default_: false    




### &nbsp;&nbsp;&nbsp;- `env_var`
Pass parameter as specified environment variable. So it would be accessible from your script.  
For example, if `"env_var": "MY_PARAM"`, you can use `echo "$MY_PARAM"` in your bash script  
Please note, that all parameters are *additionally* passed as environment variables anyway, and `env_var` property allows to override the default name. For details, please check [the ticket](https://github.com/bugy/script-server/issues/181#issuecomment-568915216)

_Required_: no  
_Type_: string  
_Default_: "PARAM_{capitalized parameter name with underscores}"  

---  

## Parameter types
### text
Simple text field. The value is passed to a script as it is.  
If parameter is `secure`, text field on UI will be masked.  
_Default value type_: string  

---  

### int
The same as text, but performs number validation.  
Can be constrained with `min` and `max` properties  
_Default value type_: string or integer  

Specific _int_ fields:  
#### `min`  
Mininal allowed value  

_Required_: no  
_Type_: int, string  

#### `max`
Maximal allowed value  

_Required_: no  
_Type_: int, string  

---  

### boolean (no_value)
Parameter is shown to a user as a checkbox. 
Since this is supported only for `no_value` parameters (flags only), value is not passed to a script  
_Default value type_: boolean  

---  

### list
Provides to user a list with allowed values (as a combobox).  
Allowed values should be specified with `values` property  
_Default value type_: string

Specific _list_ fields:

#### `values`
List of allowed values for the parameter. Can be either predefined values or a result from another script invocation  
If values are read from a script, other parameter values can be injected with `${parameter name}` syntax  

_Required_: *yes*  
_Type_: array or object  
_Example1_: `"values": [ "Apple", "Orange", "Banana" ]`  
_Example2_: `"values": { "script": "ls /home/me/projects" }`  
_Example3_: `"values": { "script": "ls /${param 1}/${param 2}" }`  

  
---  

### multiselect
Provides to user a list with allowed values (as a combobox), where he can select multiple options  
Depending on the configuration, these values will be passed as a single argument or multiple arguments  
Allowed values should be specified with `values` property  
_Default value type_: string

Specific _multiselect_ fields:

#### `values`  
See [list.values](#values)      


#### `separator`  
Defines values separator if they are passed as a single argument (e.g. comma-separated)  

_Required_: no    
_Default_: ","  
_Type_: string  

#### `multiple_arguments`
Defines, if each value should be passed as a separate argument.  
In this case `separator` property is ignored

_Required_: no  
_Default_: false  
_Type_: boolean  


---  

### file_upload
Allows user to upload a file to the server as a parameter.  
The file is uploaded to the server and stored in a temp folder. Then the file is passed to a script as an absolute path  
`secure` flag has no effect for this type  
_Default value type_: unsupported  

---  

### server_file
Allows user to select a file on server.
It can be recursive (user can navigate in folders) or non-recursive (plain list of files).
The value passed to a script as a full path, including `file_dir` parameter.     
_Default value type_: string (non-resursive) or array (recursive path)

Specific _server_file_ fields:

#### `file_dir`
Defines the server directory, where files are located    
Can be relative path to [working_directory](#working_directory)

_Required_: *yes*  
_Type_: string  

#### `file_recursive`
Defines if a user will be able to navigate between folders and select any path  
If true, file chooser dialog is shown to a user with file structure of the server.   
User will be able to navigate only within `file_dir` directory. Any other locations will be hidden.  
If this setting is true, then parameter default value should be specified as an array   

_Required_: no  
_Default_: false  
_Type_: boolean  

#### `file_extensions`  
If set, then only files with the specified extensions will be shown to a user.  
Multiple extensions could be specified.  
If the option is set, then folder selection is not possible (i.e. `file_type` option is set to file)  
Format: just extension name, without a dot. Case insensitive

_Required_: no  
_Default_: any extension  
_Type_: array (or a string for a single extension)  
   

#### `file_type`
Specifies, what type of files the user will be able to select: file or dir.  

_Required_: no  
_Allowed values_: "file" and "dir"  
_Default_: any type  
_Type_: string  

---  

### ip  
Requires user to specify a valid IP address as a parameter. Accepts either IPv4 or IPv6.  
If only only version should be accepted, there are corresponding parameter types ip4 and ip6  
Only IP lexical validation is performed and address is not checked for being accessible. 

