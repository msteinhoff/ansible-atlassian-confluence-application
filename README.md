# atlassian-service-confluence

This role installs and configures an Atlassian Confluence instance.

## Role Variables

This role comes with sensible defaults for almost all values.

The only variable required to be set is the path to the installed JDK.

### General behavior

- `confluence_user_name`

    The user name under which the application will be installed. The account will
    be created.

    **Default:** `confluence`

- `confluence_group_name`

    The group name under which the application will be installed. The group will
    be created.

    **Default:** `confluence`

- `confluence_service_name`

    The service name to use for the application.

    **Default:** `confluence`

- `confluence_perform_installation`

    When set to `true`, the role perform an installation or upgrade if the
    configured Confluence version does not match the current Confluence version on
    the host.

    This can be used as a safety mechanism so confluence is not accidentally
    changed. It could e.g. be enabled only in a special, separate playbook for
    installations.

    **Default:** `false`

- `confluence_start_after_installation`

    When set to `true`, the Confluence instance will be started after the role has
    finished installation/upgrade.

    **Default:** `false`

- `confluence_provide_root_environment`

    When set to `true`, some preconfigured aliases are provided to the root shell
    for easier navigation to Confluence-specific directories.

    **Default:** `false`

- `confluence_start_during_boot`

    When set to `true`, Confluence will be automatically started during the boot
    process.

    **Default:** `false`, should be set to `true` in production environments.

- `confluence_restart_after_reconfiguration`

    When set to `true`, Confluence will be automatically restarted when the
    configuration was changed by the ansible role.

    This can be used as a safety mechanism so confluence is not accidentally
    restarted.

    TODO: Check use cases in which this option makes sense.

    **Default:** `false`

### Installation

- `confluence_installation_version`

    Confluence version to install.

    **Default:** `5.9.5`

- `confluence_installation_archive_url`

    HTTP URL where to get the tar.gz archive for the Confluence application.

    Usually you don't need to set this.

    Depending on the environment, a HTTP proxy configuration may be needed in
    ansible.

    **Default:** `https://www.atlassian.com/software/confluence/downloads/binary/atlassian-confluence-{{ confluence_installation_version }}.tar.gz`

- `confluence_installation_archive_checksum`

    A checksum of the tar.gz archive for the given version.

    Should always be updated in tandem with  `confluence_installation_version`.

    **Default:** `sha1:4c8e877122686d138bf5588ae3ca8d53ead70030`

- `confluence_installation_software_directory`

    Path to a directory where the Confluence instance should be installed.

    **Default:** `/opt/atlassian/confluence`

- `confluence_installation_jdbc_driver_jar_path`

    Optional path to a JDBC driver jar file that will be copied into the
    Confluence instance lib directory.

    **Default:** Empty

### Configuration

- `confluence_config_jre_directory`

    Path to the directory that contains the Oracle JDK 1.8.

    **Default:** Empty, must be given by the user.

- `confluence_config_home_directory`

    Path to the Confluence home directory.

    **Default:** `/var/atlassian/application-data/confluence`

- `confluence_config_jvm_heap_size`

    How much memory the JVM should use.

    This is passed to the `-xXs` and `-Xmx` JVM options.

    **Default:** `2048m`

- `confluence_config_jvm_opts: ''

    Additional JVM configuration options.

    **Default:** Empty

- `confluence_config_tomcat_context_path`

    The tomcat context path for the Confluence instance.

    **Default:** Empty (Confluence is reachable at /).

- `confluence_config_tomcat_connectors`

    A dictionary with tomcat connector configurations. The dictionary can contain
    an arbitrary number of connectors with arbitrary  key names.

    **Default:**

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

# Example Playbook

    - hosts: servers
      roles:
         - role: ansible-atlassian-confluence-application
           confluence_config_jre_directory: '/usr/java/jdk1.8.0_72'

# License

MPLv2
