# Cisco ASA Policy Tester

## Description
This python3 tool will log into a Cisco ASA and perform a series of packet-tracer commands to test a security policy.

The tool will create a report in `reports/html_report.html`.
It is overwritten with each run.

At this time the tool requires a privilege-level 15 user.  It is untested with lower levels or passing into enable mode.

## Requirements
- netmiko
- scp>=0.10.0
- pyyaml
- pyserial
- textfsm
- logzero
- getpass3
- jinja2

## ToDo
- Impliment a retest.yml file from a test to allow for easy rerun on failed items with the need to retest all passed items.
- Test and inpliement enable password.
- Fix report ICMP type and code fields.

## Usage
- `./tester.py -i <IP> -u <USERNAME> -p -y <yml_definition>`
- `./tester.py -i 192.168.1.1 -u admin -p -y firewall_test.yml`

The tool uses GetPass3 to securely obtain the users passwords.

## YAML Structure
Store in `tests/` folder.
```
INSIDE_INTERFACE:
    allow:
        - {
            protocol: tcp, 
            icmp_type: ,
            icmp_code: ,
            source_ip: 192.168.1.1,
            source_port: 12345, 
            destination_ip: 192.168.1.2, 
            destination_port: 80
        }
        - {
            protocol: udp, 
            icmp_type: ,
            icmp_code: ,
            source_ip: 192.168.1.1,
            source_port: 12345, 
            destination_ip: 192.168.1.2, 
            destination_port: 8080
        }
        - {
            protocol: icmp, 
            icmp_type: 8,
            icmp_code: 0,
            source_ip: 192.168.1.1,
            source_port: , 
            destination_ip: 192.168.1.2, 
            destination_port: 
        }

    drop:
        - {
            protocol: tcp, 
            icmp_type: ,
            icmp_code: ,
            source_ip: 192.168.1.1,
            source_port: 12345, 
            destination_ip: 192.168.1.2, 
            destination_port: 14143
        }
        - {
            protocol: tcp, 
            icmp_type: ,
            icmp_code: ,
            source_ip: 192.168.1.1,
            source_port: 12345, 
            destination_ip: 192.168.1.2, 
            destination_port: 443
        }
```

## Running the tool
```
$ clear; ./tester.py -i 192.168.1.1 -u admin -p -y orion.yml
Password:
[I 180315 18:11:48 tester:163] Attempting connection to 192.168.1.1
[I 180315 18:11:54 tester:167] Connected to 192.168.1.1

[I 180315 18:11:54 tester:172] Processing INSIDE_INTERFACE tests
[I 180315 18:11:54 tester:81] Starting tests that should [PASS] block
[I 180315 18:11:54 tester:88] Processing tcp protocol policy
[I 180315 18:11:54 tester:102] Processing: Pass Test, "packet-tracer input INSIDE_INTERFACE tcp 192.168.1.1 12345 192.168.1.2 80 detail"
[I 180315 18:11:55 tester:110] ASA reports: allow
[I 180315 18:11:55 tester:116] Test passed!

[I 180315 18:11:55 tester:88] Processing udp protocol policy
[I 180315 18:11:55 tester:102] Processing: Pass Test, "packet-tracer input INSIDE_INTERFACE udp 192.168.1.1 12345 192.168.1.2 8080 detail"
[I 180315 18:11:55 tester:110] ASA reports: drop
[I 180315 18:11:55 tester:113] Drop reason: (acl-drop) Flow is denied by configured rule
[E 180315 18:11:55 tester:119] Test failed!

[I 180315 18:11:55 tester:88] Processing icmp protocol policy
[I 180315 18:11:55 tester:102] Processing: Pass Test, "packet-tracer input INSIDE_INTERFACE icmp 192.168.1.1 0 3 192.168.1.2 detail"
[I 180315 18:11:56 tester:110] ASA reports: allow
[I 180315 18:11:56 tester:116] Test passed!

[I 180315 18:11:56 tester:83] Starting tests that should [FAIL] block
[I 180315 18:11:56 tester:88] Processing tcp protocol policy
[I 180315 18:11:56 tester:102] Processing: Pass Test, "packet-tracer input INSIDE_INTERFACE tcp 192.168.1.1 12345 192.168.1.2 12345 detail"
[I 180315 18:11:56 tester:110] ASA reports: drop
[I 180315 18:11:56 tester:113] Drop reason: (acl-drop) Flow is denied by configured rule
[I 180315 18:11:56 tester:116] Test passed!

[I 180315 18:11:56 tester:88] Processing tcp protocol policy
[I 180315 18:11:56 tester:102] Processing: Pass Test, "packet-tracer input INSIDE_INTERFACE tcp 192.168.1.1 12345 192.168.1.2 443 detail"
[I 180315 18:11:57 tester:110] ASA reports: allow
[E 180315 18:11:57 tester:119] Test failed!

[D 180315 18:11:57 report:19] HTML report output to "/Coding/GitHub/cisco-asa-policy-tester/reports/html_report.html"
```

## Report Output
![alt text](https://i.imgur.com/i31ZbHV.png "Report Output")