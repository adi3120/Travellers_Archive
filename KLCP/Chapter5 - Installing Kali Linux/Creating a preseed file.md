
### 5.3.2. Creating a Preseed File

A preseed file is a plain text file in which each line contains the answer to one Debconf question. A line is split across four fields separated by white space (spaces or tabs). For instance, `d-i mirror/suite string kali-rolling`:

- The first field indicates the owner of the question. For example, "d-i" is used for questions relevant to the installer. You may also see a package name, for questions coming from Debian packages (as in this example: `atftpd atftpd/use_inetd boolean false`).
- The second field is an identifier for the question.
- The third field lists the type of question.
- The fourth and final field contains the value for the expected answer. Note that it must be separated from the third field with a single space; additional space characters are considered part of the value.

The simplest way to write a preseed file is to install a system by hand. Then the `debconf-get-selections --installer` command will provide the answers you provided to the installer. You can obtain answers directed to other packages with `debconf-get-selections`. However, a cleaner solution is to write the preseed file by hand, starting from an example and then going through the documentation. With this approach, only questions where the default answer needs to be overridden can be preseeded. Provide the `priority=critical` boot parameter to instruct Debconf to only ask critical questions, and to use the default answer for others.