# ansible-atlassian-confluence-application

Ansible role which installs and configures an Atlassian Confluence instance.

# Dependencies

## Oracle Java JDK

This role requires the Java JDK to be installed. The exact path can be
configured, see below for variables.

You can use `ansible-java-oracle-jdk` for this.

## Oracle Java default keystore

When using self signed certificates, it may be required to add those as trusted
certificates in the default keystore.

You can use `ansible-java-keystore` for this.

## RDBMS

A relational database is required for Confluence, e.g. MySQL, Postgres or
Oracle.

You can use `ansible-mysql-server` for for this.

## JDBC driver

Confluence needs a JDBC driver to connect to the database, which may not be
provided by the Confluence distribution. The exact path must be configured.

You can use `ansible-mysql-jdbc-driver` to install the MySQL JDBC driver.

## Packetfilter

It may be need to configure packetilter rules for incoming or outgoing network
connections.  

# Role Variables

Available variables are listed below, along with default values (see
`defaults/main.yml`).  

All variables have set sensible defaults and usually should not need any
configuration, The only variable required to be set is the path to the installed
JDK.  

## General settings

    confluence_user_name: confluence

User name under which the application will be installed. The account will be
created if it does not exist.  

    confluence_group_name: confluence

Group name under which the application will be installed. The group will be
created if it does not exist.

    confluence_service_name: confluence

Service name of the application.

    confluence_provide_root_environment: false

Provide preconfigured aliases to the root shell for easier navigation to
Confluence-specific directories.

    confluence_start_on_boot :false

Start Confluence automatically at boot.

Set this to true in production environments. Even better, use a process
supervisor e.g. systemd or monit.

## Installation variables

    confluence_perform_installation: false

Install or upgrade Atlassian Confluence automatically.

Upgrade is the same as installation, but the previously installed version
will not be removed. If the configured Confluence version does not match the
current Confluence version on the host, the configured Confluence version
will be installed.

This flag can be used as an additional safety mechanism so the Confluence
installation is not accidentally changed. It could e.g. be enabled only in
a special, separate playbook for installations.

    confluence_start_after_installation: false

Start Confluence instance after the role has finished an installation or an
upgrade.

This is handy if you want to restore a backup before the instance is started.

    confluence_installation_archive_url: https://www.atlassian.com/software/confluence/downloads/binary/atlassian-confluence-5.9.5.tar.gz

HTTP URL where to get the tar.gz archive for the Confluence application.

Depending on the environment, a HTTP proxy configuration may be needed in
ansible.

    confluence_installation_archive_name: atlassian-confluence-5.9.5

Folder name of the extracted Confluence instance.

    confluence_installation_archive_checksum: sha1:4c8e877122686d138bf5588ae3ca8d53ead70030

Checksum of the tar.gz archive for the given version.

    confluence_installation_software_directory: /opt/atlassian/confluence

Path to a directory where the Confluence instance should be installed.

    confluence_installation_jdbc_driver_jar_source_path: (empty)

Optional path to a JDBC driver jar file on the control machine that will be
copied into the Confluence instance lib directory on the remote machine.

## Configuration variables

    confluence_config_jre_directory: ''

Required path to the directory that contains the Oracle JDK 1.8.

    confluence_config_home_directory: /var/atlassian/application-data/confluence

Path to the Confluence home directory.

    confluence_config_jvm_heap_size: 1024m

How much memory the JVM should use.

This is passed to the `-xXs` and `-Xmx` JVM options.

    confluence_config_jvm_opts: ''

Additional JVM configuration options.

    confluence_config_tomcat_context_path: ''

The tomcat context path for the Confluence instance. By default,  Confluence is
reachable at /.

    confluence_config_tomcat_connectors:
      standalone:
        enabled: true
        type: standalone
        listening_port: 8090
      reverse_proxy:
        enabled: false
        type: proxy
        listening_port: 8091
        proxied_hostname: jira.example.org
        proxied_port: 443

Dictionary with tomcat connector configurations. The dictionary can contain an
arbitrary number of connectors with arbitrary names.

# Example Playbook

    - hosts: confluence-servers
      roles:
         - role: ansible-atlassian-confluence-application
           confluence_config_jre_directory: '/usr/java/jdk1.8.0_72'

# License

MPLv2
