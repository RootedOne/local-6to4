## About The Project

When setting up an IPv6 GRE tunnel between two servers, one server (Server 2) may not have direct IPv6 connectivity. In this case, a local IPv6 address is assigned to the tunnel interface on Server 2 for the following reasons:

* Enabling IPv6 Connectivity: Without a local IPv6 address, Server 2 would not be able to communicate over IPv6.

* Routing and Addressing: The local IPv6 address serves as the endpoint for IPv6 communication, allowing other devices to route IPv6 traffic to Server 2 through the GRE tunnel.

* Network Configuration: Applications and services on Server 2 may require IPv6 addresses for proper operation.

The choice of using a GRE (Generic Routing Encapsulation) tunnel is made because:

* IPv6 Transition Mechanism: GRE tunnels allow IPv6 traffic to traverse IPv4-only networks by encapsulating IPv6 packets within IPv4 packets.

* Simplicity and Compatibility: GRE tunnels are widely supported and relatively simple to configure.

* Flexibility and Control: GRE tunnels offer flexibility in routing, addressing, and encapsulation control.

* Security and Isolation: GRE tunnels can be combined with security mechanisms like IPsec and provide traffic isolation.

### Prerequisites

* Two servers (Server 1 and Server 2) with Ubuntu installed

* install iproute2 on both servers
  ```sh
  sudo apt install iproute2
  ```

### Server 1 (with IPv6 connectivity)

1. Create a new file in the `/etc/netplan` directory named `gre-tunnel.yaml`:
   ```sh
   sudo nano /etc/netplan/gre-tunnel.yaml
   ```
2. Add the following content to the file, replacing the placeholders with the appropriate values:
```sh
    network:
  version: 2
  renderer: networkd
  ethernets:
     Your existing Ethernet interface configuration goes here
     ...

  tunnels:
    gre1:
      mode: gre
      remote: [Server2_IPv4_Address]
      local: [Server1_IPv4_Address]
      addresses:
        - [Server1_IPv6_Tunnel_Address]/64
  ```
3. Save and close the file.

4. Apply the new configuration:
    ```sh
    sudo netplan apply
    ```


## Server 2 (without IPv6 connectivity) 

1. Create a new file in the `/etc/netplan` directory named `gre-tunnel.yaml`:

2. Add the following content to the file, replacing the placeholders with the appropriate values:
    ```sh
    network:
  version: 2
  renderer: networkd
  ethernets:
    # Your existing Ethernet interface configuration goes here
    # ...

  tunnels:
    gre1:
      mode: gre
      remote: [Server1_IPv4_Address]
      local: [Server2_IPv4_Address]
      addresses:
        - [Server2_IPv6_Tunnel_Address]/64
    ```
3. Save and close the file.

4. Apply the new configuration:
    ```sh
    sudo netplan apply
    ```

### Verification

After applying the configuration on both servers, the IPv6 GRE tunnel should be automatically established and persistent across reboots.

You can verify the tunnel interface status and connectivity using the following commands:
    ```sh
    ip -6 addr show
    ip -6 route show
    ip -6 neigh show
    ```

### Note

_The `netplan` configuration syntax may vary slightly depending on your Ubuntu version. If you encounter any issues, consult the official Ubuntu documentation for your specific version.

### Roadmap

- [x]  Set up development environment
- [x] Define project requirements and specifications
- [x] Design system architecture
- [ ] Implement core functionality
- [ ] Conduct thorough testing and debugging
- [ ] Integrate additional features and enhancements

## Acknowledgments

Use this space to list resources you find helpful and would like to give credit to. I've included a few of my favorites to kick things off!

* [Configuring tunnels with iproute2](https://www.deepspace6.net/docs/iproute2tunnel-en.html)
