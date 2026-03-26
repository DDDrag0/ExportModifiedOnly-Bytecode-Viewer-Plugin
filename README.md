> ⚠️ **The standalone plugin is deprecated and removed**
> 
> Due to technical limitations, the standalone JAR is no longer supported.  
> **Please use the pre‑integrated BCV build** available at:  
> [https://github.com/DDDrag0/bytecode-viewer/releases](https://github.com/DDDrag0/bytecode-viewer/releases)
> 
> This repository is kept as a reference for the source code.
---
# Export Modified Only - Bytecode Viewer Plugin

**Export Modified Only** is a plugin for [Bytecode Viewer (BCV)](https://github.com/Konloch/bytecode-viewer) 2.12 that exports only the classes modified during a reverse engineering session, preserving the full package structure (e.g., `a/b/c/d/ModifiedClass.class`). Instead of saving the entire archive (JAR/APK), this plugin creates a clean ZIP file containing exclusively the changed classes – ideal for producing minimal patches or testing modifications.

### What it does
- Creates a ZIP archive containing **only the classes that have changed** (no untouched files).
- Maintains the original directory hierarchy (e.g., `a/b/c/MyClass.class`).
- Automatically places the ZIP in the same folder as your last manual save (`Ctrl+S` or `File → Save As…`).
- The filename includes a timestamp for uniqueness: `modified_classes_YYYYMMDD-HHmmss.zip`.

## Usage

### First Run – Capture Initial State
- Open your target JAR/APK in BCV.
- From the `Plugins` menu, select **Export Modified Only**.  
  A console opens, showing the number of monitored classes and instructions.
- The plugin has now stored the original bytecode of every class.

### Modify and Save
- Make your desired changes to one or more classes.
- **Save the whole workspace** using `Ctrl+S` or `File → Save As…`.  
  (This step is essential – the plugin uses the directory of this save as the output location.)

### Second Run – Export Modified Classes
- Run **Export Modified Only** again from the `Plugins` menu.
- The plugin compares the current bytecode with the saved state.
- If changes are detected, a ZIP file named `modified_classes_YYYYMMDD-HHmmss.zip` is created in the same directory as your last save.  
  The console lists all exported classes.

If no modifications are found, the console simply reports `No modified classes detected.`

The plugin console displays:
- The number of monitored classes.
- The list of modified classes.
- The final path of the generated ZIP.

---

## Integrating the Source into a BCV Development Environment

1. **Clone the BCV repository** and check out the desired version (e.g., `v2.12`):
   ```bash
   git clone https://github.com/Konloch/bytecode-viewer.git
   cd bytecode-viewer
   git checkout v2.12
   ```

2. **Copy `ExportModifiedOnly.java`** into the preinstalled plugins directory:
   ```
   src/main/java/the/bytecode/club/bytecodeviewer/plugin/preinstalled/
   ```

3. **Register the plugin** in `MainViewerGUI.java`:
   - Add the import:
     ```java
     import the.bytecode.club.bytecodeviewer.plugin.preinstalled.ExportModifiedOnly;
     ```
   - Declare a menu item (e.g., among the other plugin fields):
     ```java
     public final JMenuItem exportModifiedOnly = new TranslatedJMenuItem("Export Modified Only", TranslatedComponents.EXPORT_MODIFIED_ONLY);
     ```
   - In the `buildPluginMenu()` method, add:
     ```java
     pluginsMainMenu.add(exportModifiedOnly);
     exportModifiedOnly.addActionListener(arg0 -> PluginManager.runPlugin(new ExportModifiedOnly()));
     ```
   - In the `the.bytecode.club.bytecodeviewer.translation.TranslatedComponents` class, add the constant:
     ```java
     EXPORT_MODIFIED_ONLY("Export Modified Only"),
     ```

4. **Rebuild BCV** with Maven:
   ```bash
   mvn clean package
   ```

The plugin will then be permanently available in your custom BCV build.

---

## Troubleshooting & Known Issues

| Problem | Likely Cause | Solution |
|---------|--------------|----------|
| `java.lang.NullPointerException` when pressing "Run" in the plugin editor | The source file was not saved before execution. | Use `Save As…` in the editor to save the file to disk, then click "Run". |
| Compilation errors with generics (e.g., `Map.Entry<String, ClassNode>`) | BCV’s internal script compiler (Janino) does not fully support generics. | **Use the pre‑compiled JAR** instead of running the `.java` file as a script. |
| Plugin does not appear in the `Plugins` menu after copying the JAR | The JAR was not placed in the correct `plugins` folder, or BCV was not restarted. | Verify the folder path and restart BCV. |
| ZIP file is saved in the wrong directory | The directory from `Configuration.getLastSaveDirectory()` may not be the one you expected. | Ensure you have performed a manual save (`Ctrl+S` or `File → Save As…`) before the second plugin run. |

---

## License
This plugin is distributed under the **GNU General Public License v3.0**, the same license as Bytecode Viewer. See the [LICENSE](LICENSE) file for details.

---

## Contributing
Contributions, issues, and feature requests are welcome. Feel free to open an issue or submit a pull request on GitHub.

**Maintainer:** Michele D'Arienzo  
**Project Link:** [https://github.com/DDDrag0/ExportModifiedOnly-Bytecode-Viewer-Plugin](https://github.com/DDDrag0/ExportModifiedOnly-Bytecode-Viewer-Plugin)
