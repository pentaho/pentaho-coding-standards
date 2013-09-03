Pentaho enforces coding standards and style through the CheckStyle project ( http://checkstyle.sf.net ). 

CheckStyle will be integrated into our build system soon. Until then, to ensure your contributions 
conform to the standard, you'll need to configure your IDE using the instructions below.


Installation (Eclipse):

1. Install CheckStyle plugin http://eclipse-cs.sf.net/update/
2. Configure the plugin to use the pentaho_checkStyle.xml
3. Configure the Eclipse Code Formatter to use the attached pentaho_formatter.xml

The plugin documentation is here if you need it: http://eclipse-cs.sourceforge.net

Installation (IntelliJ):

Install CheckStyle-IDEA and Eclipse Code Formatter plugins. Both are available in the default public repo.
Configure the pentaho_checkStyle.xml for CheckStyle and pentaho_formatter.xml for the Eclipse Code Formatter
You should now be able to auto format and receive CheckStyle violation notifications.
