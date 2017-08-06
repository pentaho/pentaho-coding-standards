# Introduction to Pentaho Coding Standards

Pentaho enforces coding standards and style through the [CheckStyle project](http://checkstyle.sf.net).

CheckStyle will soon be integrated into our build system. Until that time, you'll need to configure your IDE using the instructions below to to ensure your contributions conform to the standard.

### Installation (Eclipse):
1. Install [CheckStyle plugin](http://eclipse-cs.sf.net/update/)
2. Configure the plugin to use the `pentaho_checkStyle.xml`.
 - Give it the raw github URL: `https://raw.githubusercontent.com/pentaho/pentaho-coding-standards/master/pentaho_checkStyle.xml`
 - That will also dynamically retrieve the Pentaho `suppressions.xml` referenced in the checker
3. Configure the Eclipse Code Formatter to use the attached `pentaho_formatter.xml`.

The Eclipse CheckStyle plugin [documentation](http://eclipse-cs.sourceforge.net) my be helpful.


### Installation (IntelliJ):
1. Install the `CheckStyle-IDEA` plugin.
 - Available in the default public repository
 - Latest versions require _Java 8 JDK_ to be installed in your machine
2. Clone this repository to your machine.
3. Configure the plugin:
 - Go to `Preferences` -> `Other Settings` -> `Checkstyle`
 - Add the Pentaho configuration file `pentaho_checkStyle.xml`
 - Set the property `samedir` to `.`
4. Configure `Code Style` _(to enable auto formatting)_:
 - Go to `Preferences` -> `Editor` -> `Code Style`
 - Click on `Manage...` in the `Scheme` section
 - Select the **Project** scheme
 - Click on `Import...` and choose to import from `IntelliJ IDEA code style XML`
 - Select the file `codeStyleSettings.xml` in the cloned repository directory
 - Check the box to import to the selected scheme _(Project)_
5. Apply the new settings.


## Additional Information
[CheckStyle Tips](https://github.com/pentaho/pentaho-coding-standards/wiki/CheckStyle-Tips) - Learn how to suppress CheckStyle modules for select areas.
