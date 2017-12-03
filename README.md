# vagrant-geonetwork

Vagrantfile that provides a [GeoNetwork opensource](https://geonetwork-opensource.org/) environment based on ubuntu/xenial with inline shell provision with the following components installed:

- OpenJDK 1.8
- Tomcat 8
- Postgres 9.5

This environment is intended only for testing/development, not for production.

## Installation and usage

Install [Vagrant](https://www.vagrantup.com/) and [Virtual Box](https://www.virtualbox.org/).

Run the following to command to clone the repository:

    # Clone it locally:
    $ git clone https://github.com/josegar74/vagrant-geonetwork

    # Enter the cloned directory:
    $ cd vagrant-geonetwork

Usage: 

    # Start up the virtual machine:
    $ vagrant up

    # Stop the virtual machine:
    $ vagrant halt

To access GeoNetwork from your browser: http://localhost:8080/geonetwork

## Configuration

Edit the file Vagrantfile to customise the default values.

Default configuration:

- Database

    - User: geonetwork
    - Password: geonetwork
    - Database: geonetwork

- Networking: NAT

    - Port forwarding: Host 8080 --> Guest 8080 (Tomcat) 
    - Port forwarding: Host 15432 --> Guest 5432 (Postgres) 

### License

This is released under the MIT license. See the file [LICENSE](LICENSE).

[Virtual Box]: https://www.virtualbox.org/
[Vagrant]: http://www.vagrantup.com/
