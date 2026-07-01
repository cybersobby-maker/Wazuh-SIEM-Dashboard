# Wazuh-SIEM-Dashboard

Wazuh SIEM Deployment | Home Security Lab

Self directed home lab project simulating a small enterprise network for hands-on SIEM configuration, log correlation, and threat detection practice.

Overview:

This project documents the build of a virtualized, multi-machine Windows/Linux lab environment with a centralized Wazuh SIEM deployment. The goal was to gain practical, hands on experience with the tools and workflows used by SOC analysts including log ingestion, detection rule writing, alert tuning, and incident triage, outside of a guided course environment.

Lab Architecture:

Hypervisor: VMware Workstation SIEM: Wazuh (Manager + Indexer + Dashboard) Endpoints: Windows 10/11 VM, Ubuntu/Linux VM, running the Wazuh agent Network: Isolated virtual network (host-only/NAT) to safely simulate attacker behavior without touching the host machine or internet-facing systems

What I Built:

![image alt](https://github.com/cybersobby-maker/Wazuh-SIEM-Dashboard/blob/main/SIEM%20Dashboard%20(View).png?raw=true)

Deployed and configured Wazuh as the central log collection and correlation point for all endpoints in the lab. Wrote custom detection rules and alert workflows for indicators such as repeated failed login attempts and privilege escalation events. Built a multi-machine Windows + Linux environment to mirror a realistic small-network topology for testing. Hardened endpoint and network configurations based on vendor documentation and security best practices. Practiced log analysis and threat detection workflows end-to-end: generate an event -> confirm it's captured -> confirm it correlates/alerts correctly -> tune the rule if noisy or missed.

Example Problem: Detecting Repeated Failed Logins

Simulated repeated failed SSH/RDP login attempts against an endpoint. Confirmed Wazuh agent forwarded the relevant auth logs to the manager. Built/adjusted a custom rule to flag the threshold of failed attempts within a time window. Verified the alert fired correctly in the Wazuh dashboard, including source IP, target account, and timestamp.

Skills Demonstrated:

SIEM deployment & administration (Wazuh) Custom detection rule / alert logic Log monitoring & analysis Windows and Linux system administration Virtual network design and segmentation Self-directed troubleshooting using vendor docs, community resources, & AI 

Tools Used: Wazuh, Kali Linux, Wireshark, Nmap, tcpdump, VMware Workstation, Windows, Linux, Ubuntu

Notes / Lessons Learned:

Midway through this build, the Dashboard started throwing "API connection down or inaccessible" errors, the agent could no longer talk to the Manager. I initially assumed the Dashboard configuration was the problem and spent hours poking at "wazuh.yml", uninstalling and installing packages, updating OS systems, and dismantling my configuration before checking the Manager side. With the help of AI I came to the solution of Running "systemctl status wazuh-manager" and tailing "/var/ossec/logs/api.log" showing the real issue: the "wazuh-apid service" had crashed after I rotated the "wazuh-wui" API password without updating the matching entry in the Dashboards config. Every auth attempt was failing until it hit a login attempt lockout on top of it. Next time I'd verify any credential change with a "raw curl call" before touching the Dashboard at all. Bigger lesson: The error shows up wherever the symptom is visible, not where the cause is you have to walk the chain of processes. (Dashboard -> API -> Manager daemons -> disk/permissions) instead of fixating on the layer complaining loudest.

General lessons learned

Version match matters. Manager, Indexer, and Dashboard should share at least major. minor version mismatches cause vague API errors.

Startup order: Indexer -> Manager -> Dashboard.

The visible error isnt always the cause, Go through the processes bottom up and vice versa instead of assuming its the Dashboard.

Watch disk space on the Manager, a full partition can crash "wazuh-analysisd" and corrupt Dashboard registry files.

KNOW YOUR IMPORTANT LOGS: /var/ossec/logs/api.log (API), /var/ossec/logs/ossec.log (Manager), journalctl -u wazuh-dashboard, /var/log/wazuh-indexer/.

Status: SIEM is Ongoing actively expanding detection coverage while working through CyberDefenders / Blue Team Labs Online exercises in parallel.
