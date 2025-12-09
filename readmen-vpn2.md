This is a slightly more complex configuration because of how Docker networking works with VPN containers, but it is the "Holy Grail" setup for many users.

Here is the logic for the file below:

    The Network (arr_net): We still use the custom network (172.28.0.x).

    Regular Apps: Radarr, Sonarr, Homarr, etc., are connected directly to the network with their own Static IPs. They do not go through the VPN.

    Gluetun (VPN): This container joins the arr_net and takes the static IP 172.28.0.7.

    qBittorrent: This uses network_mode: "service:gluetun". It has no IP address of its own. It effectively "becomes" localhost inside the Gluetun container.

        To access qBittorrent WebUI: You talk to the Gluetun IP (172.28.0.7).

Critical Configuration Details

    Setting up Sonarr/Radarr/Prowlarr:
    When you go into your Apps (like Sonarr) to add your Download Client (qBittorrent), do not use localhost or 172.28.0.3.

        Host: 172.28.0.7 (This is the IP of Gluetun, where qBittorrent lives).

        Port: 8080

    Setting up Homarr:
    When adding qBittorrent to your dashboard:

        Internal Address: http://172.28.0.7:8080

    Port Mapping:
    Notice that the qbittorrent section has no ports listed. This is intentional. The ports 8080 and 6881 are listed under gluetun. This ensures traffic enters the VPN container first, travels through the tunnel, and then reaches qBittorrent.
