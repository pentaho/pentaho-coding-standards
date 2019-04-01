# Introduction to Hitachi Vantara Coding Standard s  
Please check the [wiki](https://github.com/pentaho/pentaho-coding-standards/wiki) for more information on how to structure your project so that it conforms with our standards.

## CheckStyle
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
