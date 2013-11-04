Pentaho enforces coding standards and style through the CheckStyle project ( http://checkstyle.sf.net ). 

CheckStyle will soon be integrated into our build system. Until that time, you'll need to configure your IDE using the
instructions below to to ensure your contributions 
conform to the standard.


Installation (Eclipse):

1. Install CheckStyle plugin http://eclipse-cs.sf.net/update/
2. Configure the plugin to use the pentaho_checkStyle_.xml
3. Configure the Eclipse Code Formatter to use the attached pentaho_formatter.xml

The plugin documentation is here if you need it: http://eclipse-cs.sourceforge.net

Installation (IntelliJ):

1. Install the CheckStyle-IDEA plugin. It's available in the default public repository.
2. Configure the CheckStyle-IDEA with pentaho_checkStyle.xml.
3. Copy the codeStyleSettings.xml into your IntelliJ project folder ( {{IdeaIntelliJProjectFolder}}/.idea/codeStyleSettings.xml). Overwrite if it's already there.
4. Open IntelliJ, set the Project Settings -> Code Style -> Scheme to "Project"
