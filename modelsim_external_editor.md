# ***Настройка внешнего редактора для ModelSim (QuestaSim)***

## Methode 1
Put this in a tcl file somewhere:  
```tcl
proc external_editor {filename linenumber} {
    exec "youreditor" $linenumber $filename &   # edit as required
}
set PrefSource(altEditor) external_editor
```

## Methode 2

This line only needs to be ran once. I ran it directly in Modelsim's terminal.  
```tcl
set PrefSource(altEditor) external_editor
```
This is the actual function I used

```tcl
proc external_editor {filename linenumber} {
    exec gvim +$linenumber $filename &
}
```
However, the external_editor proc still needs to be added to Modelsim. As a one off, you can actually just drop the function into the terminal, but I created a modelsim.tcl file within my home directory.

In that I linked to the external_editor.tcl file elsewhere.

```tcl
source /opt/altera/15.1/modelsim_ase/tcl/custom_scripts/external_editor.tcl
```

I suspect there is any number of ways to permanently load the external editor proc, but this is what I did.

As a side note, I would have placed this as a comment under Anon's answer, but I don't have enough reputation as I just created my account.

## Methode 3

There are two options you may use:

* PrefSource(altEditor): this replaces the internal editor when double-clicking etc., i.e. when normally the internal editor would be shown.  
* PrefMain(Editor): this sets the external editor that can be launched from some menus. It doesn't support the linenumber argument AFAICT.  

For my setup I followed these steps (to eventually launch geany as the editor):

1. To make questasim load a separate settings file at startup, set the MODELSIM_TCL environment variable to point to the path of the respective file, e.g.:

    ```tcl 
    export MODELSIM_TCL="$HOME/.mentor/perf.tcl" 
    ```
    Alternatively, you can just put the following into ~/modelsim.tcl AFAIK.

1. Add this to the settings file:
    ```tcl
    # Launch custom command as external editor:
    proc external_editor {filename} {
        exec geany "$filename" &
    }
    set PrefMain(Editor) external_editor

    # Replace the internal editor:
    proc external_editor_ln {filename linenumber} {
        exec geany "$filename:$linenumber" &
        return
    }
    set PrefSource(altEditor) external_editor_ln

    # Fix default viewers
    set PrefMain(pdfViewer) xdg-open
    set PrefMain(htmlViewer) xdg-open
    ```  
    Note the extra return startment in ``external_editor_ln`` that prohibits the function to return a value, which would create an error output ``# invalid command name "0"`` (where 0 is the return value). The last two statements are a "bonus" that for example fix launching documentation if you don't have ``acroread`` installed.  

## Methode 4
The configuration setting you are looking for can be managed in one of several ways. From what I have read, the two most common methods are by accessing either the files pref.tcl and modelsim.tcl. More info can be had if you search on either of those file names in a ModelSim ["quick guide"](./vsim_quickref.pdf)

**pref.tcl**  
* Found at /<install_dir>/modeltech/tcl/vsim/pref.tcl  
* Will always load at ModelSim launch.  

**modelsim.tcl**  
* Loads the first file found among the following:  
    1. ./modelsim.tcl, i.e. a file named as such in the current directory.  
    1. $MODELSIM_TCL if the environment variable is defined and the file exists.  
    2. $HOME/modelsim.tcl, again if the environment variable is defined.  

This is where the previous responses come into play. In either of the locations listed above, you can drop in your Tcl function. I lifted the implementation from the previous posts by Anon, J D R, stefanct, but improved upon the usage of the ``exec`` command based on another question. Certainly modifications can be made, but here's my function written specifically for Notepad++. Notice that the ``notepad++`` argument and the ``"-n..."`` tacked onto the ``linenumber`` parameter are specific to Notepad++.   

```tcl
proc external_editor {filename linenumber} {
    exec {*}[auto_execok start] notepad++ $filename "-n$linenumber"
    return
}
set PrefSource(altEditor) external_editor
```  
With this this code dropped into pref.tcl or modelsim.tcl, if you double-click in the ModelSim terminal on a line with with compilation errors, ModelSim should launch the editor of your choice to the offending line of code.

*NOTE: The reference guides mentioned above seem to not be readily available directly from Mentor Graphics, which is a Siemens property at the time of post. Most common source seems to be universities.*