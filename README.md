# aiida-template

AiiDA plugin template for testing and getting started with development

* can be pip-installed from source with no dependencies except for aiida 0.8 (or later)
* can be run on a local machine with direct scheduler for easy testing


# Installation

```shell
$ git clone --depth 1 https://github.com/aiidateam/aiida-plugin-template.git aiida-YOUR-PLUGIN-NAME
$ cd YOUR-PLUGIN-NAME
$ git mv aiida_template aiida_YOUR_PLUGIN_NAME
$ sed -i -e 's|aiida-template|aiida-YOUR-PLUGIN-NAME|g' -e 's|aiida_template|aiida_YOUR_PLUGIN_NAME|g' setup.json
$ pip install -e .  # also installs aiida, if missing (but not postgres)
$ reentry scan -r aiida
$ verdi quicksetup  # better to set up a new profile
$ verdi calculation plugins  # should now show template.multiply (an endpoint implemented in this plugin template)
```

# Usage

A complete example of how to submit a test calculation using this plugin.

```shell
$ verdi computer setup   # set up localhost for testing
At any prompt, type ? to get some help.
---------------------------------------
=> Computer name: localhost
Creating new computer with name 'localhost'
=> Fully-qualified hostname: localhost
=> Description: my local computer
=> Enabled: True
=> Transport type: local
=> Scheduler type: direct
=> AiiDA work directory: /tmp
=> mpirun command:
=> Default number of CPUs per machine: 4
=> Text to prepend to each command execution:
   # This is a multiline input, press CTRL+D on a
   # empty line when you finish
   # ------------------------------------------
   # End of old input. You can keep adding
   # lines, or press CTRL+D to store this value
   # ------------------------------------------
=> Text to append to each command execution:
   # This is a multiline input, press CTRL+D on a
   # empty line when you finish
   # ------------------------------------------
   # End of old input. You can keep adding
   # lines, or press CTRL+D to store this value
   # ------------------------------------------
Computer 'localhost' successfully stored in DB.
pk: 1, uuid: a5b452f0-ec1e-4ec2-956a-10a416f60ed3
Note: before using it with AiiDA, configure it using the command
  verdi computer configure localhost
(Note: machine_dependent transport parameters cannot be set via
the command-line interface at the moment)

$ verdi computer configure localhost 
Configuring computer 'localhost' for the AiiDA user 'leopold.talirz@epfl.ch'
Computer localhost has transport of type local
There are no special keys to be configured. Configuration completed.

$ verdi code setup  # set up (local) code
At any prompt, type ? to get some help.
---------------------------------------
=> Label: aiida_template
=> Description: aiida template plugin
=> Local: True
=> Default input plugin: template.multiply
=> Folder with the code: /your/path/to/aiida_template
=> Relative path of the executable: code.py
=> Text to prepend to each command execution
FOR INSTANCE, MODULES TO BE LOADED FOR THIS CODE:
   # This is a multiline input, press CTRL+D on a
   # empty line when you finish
   # ------------------------------------------
   # End of old input. You can keep adding
   # lines, or press CTRL+D to store this value
   # ------------------------------------------
=> Text to append to each command execution:
   # This is a multiline input, press CTRL+D on a
   # empty line when you finish
   # ------------------------------------------
   # End of old input. You can keep adding
   # lines, or press CTRL+D to store this value
   # ------------------------------------------
Code 'aiida_template' successfully stored in DB.
pk: 1, uuid: 7627c747-b7f2-4717-b0fa-94e53915e422

$ verdi run examples/submit.py
submitted calculation; calc=Calculation(uuid='a4d2fa09-f704-4d47-8d0f-07001b13316f') # ID=3

$ verdi calculation list
# Last daemon state_updater check: (Never)
  PK  State     Creation      Sched. state  Computer    Type
----  --------  ----------  --------------  ----------  ----------------
   3  TOSUBMIT  1m ago                      localhost   emplate.multiply

Total results: 1

$ verdi daemon start  # may need to 'verdi daemon configureuser'
Clearing all locks ...
Starting AiiDA Daemon ...
Daemon started

$ verdi calculation list -a # after daemon poll (30s), calculation should be "FINISHED"
# Last daemon state_updater check: 0h:00m:06s ago (at 19:36:11 on 2017-09-20)
  PK  State             Creation    Sched. state    Computer    Type
----  ----------------  ----------  --------------  ----------  ----------------
   3  FINISHED          29s ago     DONE            localhost   emplate.multiply

Total results: 1

$ verdi calculation show 3
-----------  --------------------------------------------------
type         MultiplyCalculation
pk           23
uuid         6d4b774a-7de7-485a-82b7-6a36b4a60db8
label        aiida_template computes 2*3
description  Test job submission with the aiida_template plugin
ctime        2017-09-27 13:50:14.133219+00:00
mtime        2017-09-27 13:52:35.683063+00:00
computer     [1] localhost
code         aiida_template
-----------  --------------------------------------------------
##### INPUTS:
Link label      PK  Type
------------  ----  ------------------
parameters       3  MultiplyParameters
##### OUTPUTS:
Link label           PK  Type
-----------------  ----  -------------
remote_folder         4  RemoteData
output_parameters     6  ParameterData
retrieved             5  FolderData


$ verdi data parameter show 3
{
  "x1": 2,
  "x2": 3
}

$ verdi data parameter show 26
{
  "product": 6
}

```

