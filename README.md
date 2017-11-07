# Introduction to Hitachi Vantara Coding Standards

Pentaho enforces coding standards and style through the CheckStyle project ( http://checkstyle.sf.net ).

CheckStyle will soon be integrated into our build system. Until that time, you'll need to configure your IDE using the instructions below to to ensure your contributions conform to the standard.

### Installation (Eclipse):

- Install CheckStyle plugin http://eclipse-cs.sf.net/update/
- Configure the plugin to use the `checkstyle/pentaho_checks.xml`; give it the raw github URL [here](https://raw.githubusercontent.com/pentaho/pentaho-coding-standards/master/checkstyle/pentaho_checks.xml)  
- Configure the Eclipse Code Formatter to use the attached `ide/eclipse_formatter.xml`

The Eclipse CheckStyle plugin documentation my be helpful: http://eclipse-cs.sourceforge.net

### Installation (IntelliJ):

- Install the CheckStyle-IDEA plugin. (available in the default public repository)
- Configure the CheckStyle-IDEA (Preferences -> Other Settings -> CheckStyle).
  - Select "8.0" in the "Checkstyle version" dropdown
  - Select "Only Java sources (including tests) in the "Scan Scope" dropdown
  - Click "Apply"
  - Select "Use a checkstyle file accessible via HTTP" with the raw github URL [here](https://raw.githubusercontent.com/pentaho/pentaho-coding-standards/master/checkstyle/pentaho_checks.xml)
    - Alternatively, you can select "Use a local Checkstyle file". Clone this repo and use the `checkstyle/pentaho_checks.xml`.
- Configure code style
  - Copy the`ide/intelliJ_codeStyleSettings.xml` into your IntelliJ project folder (`{{IdeaIntelliJProjectFolder}}/.idea/codeStyleSettings.xml`). Overwrite if it's already there.
  - Open IntelliJ, set the Project Settings -> Code Style -> Scheme to "Project"

### Installation (Visual Studio Code):

- Install the checkstyle extension
- Download the checkstyle-8.0-all.jar file (not any other version) from https://sourceforge.net/projects/checkstyle/files/checkstyle/8.0/ and place it in a folder
- Clone this repository and place in a folder of your choice
- Within Visual Studio Code, go to File -> Preferences -> Settings
- Edit your user settings to reflect the following:- 
```json
{
    "editor.tabSize": 2,
    "editor.wordWrap": "on",
    "editor.formatOnPaste": true,
    "editor.formatOnType": true,
    "checkstyle.configurationPath": "c:\\Users\\USERNAME\\SOMEFOLDER\\pentaho-coding-standards\\checkstyle\\pentaho_checks.xml",
    "checkstyle.checkstylePath": "C:\\Users\\USERNAME\\SOMEFOLDER\\checkstyle\\checkstyle-8.0-all.jar"
}
```
  - Note, if using Linux/Mac, use forward slashes for folders as per unix norms
- Save and close this settings file
- Now open your java file. When you next save it, checkstyle advisories will appear as green underlined warnings within source files, and within the 'Problems' report tab alongside Java compilation issues.

## Additional Information
[CheckStyle Tips](https://github.com/pentaho/pentaho-coding-standards/wiki/CheckStyle-Tips) - Learn how to suppress CheckStyle modules for select areas.
