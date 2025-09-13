Concrete implementation plan (1–2 day sprint to MVP)

tor_manager

Ship platform Tor binaries; generate torrc from profile; bring up Tor; poll ControlPort until BOOTSTRAP 100.

Export SocksPort, DNSPort, TransPort (optional) to other modules.

tun2socks

Use a Rust tun driver (Linux tun, Windows Wintun).

Bind tun2socks to Tor’s SocksPort for TCP+UDP.

IP address: assign a private /30 to TUN (e.g., 10.111.0.1/30).

route_guard + leak_killswitch

Linux: apply nftables rules; Windows: firewall rules tied to adapter/Tor process.

Verify with connectivity probes; fail-closed if Tor dies.

dns_guard

Set OS resolv (Linux) to 127.0.0.1:5353 during session; Windows use NRPT or block port 53 except TUN/Tor.

Optional: run dnscrypt-proxy behind Tor’s SOCKS (advanced profile).

profile_manager + CLI

profiles/default.toml (baseline), profiles/obfs4.toml (stealth), profiles/transproxy.toml (Linux).

CLI: torvpn start --profile obfs4, torvpn stop, torvpn status, torvpn leaktest.

How I’ll use the InviZible code specifically

Extract exact torrc options and pluggable transport declarations from the configs you saw in “Config Excerpts” and turn them into our templated profiles.

Port any iptables scripts you spotted (see “Keyword Hits”) to nftables rules (Linux) and produce equivalent Windows firewall rules.

Reuse bridge management UX/logic (file formats, rotation cadence).

Mirror bootstrap status parsing and connection retry backoff patterns.

If there’s a VpnService-style tun code path, treat it as a conceptual reference for how they handle MTU, MSS clamping, and per-app include/exclude; implement equivalent in our Rust tun2socks.