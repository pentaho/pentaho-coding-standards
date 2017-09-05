# Introduction to Pentaho Coding Standards

Pentaho enforces coding standards and style through the CheckStyle project ( http://checkstyle.sf.net ).

CheckStyle will soon be integrated into our build system. Until that time, you'll need to configure your IDE using the instructions below to to ensure your contributions conform to the standard.

### Installation (Eclipse):

- Install CheckStyle plugin http://eclipse-cs.sf.net/update/
- Configure the plugin to use the `pentaho_checkStyle.xml`; give it the raw github URL: https://raw.githubusercontent.com/pentaho/pentaho-coding-standards/master/pentaho_checkStyle.xml
  (that will also dynamically retrieve the Pentaho suppressions.xml referenced in the checker)
- Configure the Eclipse Code Formatter to use the attached pentaho_formatter.xml

The Eclipse CheckStyle plugin documentation my be helpful: http://eclipse-cs.sourceforge.net

### Installation (IntelliJ):

- Install the CheckStyle-IDEA plugin. (available in the default public repository)
- Configure the CheckStyle-IDEA (Preferences -> Other Settings -> CheckStyle).
  - Select "8.0" in the "Checkstyle version" dropdown
  - Select "Only Java sources (including tests) in the "Scan Scope" dropdown
  - Click "Apply"
  - Select "Use a checkstyle file accessible via HTTP" with the raw github URL:
    https://raw.githubusercontent.com/pentaho/pentaho-coding-standards/master/pentaho_checkStyle.xml
    - Alternatively, you can select "Usa a local Checkstyle file". Clone this repo and use the `pentaho_checkStyle.xml`.
- Configure code style
  - Copy the codeStyleSettings.xml into your IntelliJ project folder (`{{IdeaIntelliJProjectFolder}}/.idea/codeStyleSettings.xml`). Overwrite if it's already there.
  - Open IntelliJ, set the Project Settings -> Code Style -> Scheme to "Project"

## Additional Information
[CheckStyle Tips](https://github.com/pentaho/pentaho-coding-standards/wiki/CheckStyle-Tips) - Learn how to suppress CheckStyle modules for select areas.
