# P4Runtime
Notes on P4Runtime

The p4runtime-shell provides a convenient and simplified interface for interacting with P4Runtime-enabled devices via gRPC. It abstracts many details of the P4Runtime protocol and offers a variety of functions to perform operations like pipeline configuration, table entry manipulation, and counter reads.

Here is a list of common functions available in p4runtime-shell:

Key Functions in p4runtime-shell
1. set_fwd_pipe()
Configures the forwarding pipeline on the device (using the SetForwardingPipelineConfig message).
Parameters:
p4info_path: Path to the P4Info file.
bin_path: Path to the compiled P4 binary (e.g., .bin or .json).
dry_run: Boolean flag to perform a dry run without actually applying the configuration.
Example:
python
Copy code
sh.set_fwd_pipe(p4info_path='build/p4info.txt', bin_path='build/my_p4_program.bin')
2. make_table_entry()
Creates a table entry (match-action rule) that can be inserted, updated, or deleted in the switch.
Parameters:
table_name: Name of the table (from P4Info).
match_fields: Dictionary of match fields and values.
action_name: Name of the action to execute.
action_params: Dictionary of action parameters.
priority: (Optional) Priority for the table entry.
Example:
python
Copy code
table_entry = sh.make_table_entry(
    table_name="ingress.ipv4_lpm",
    match_fields={"hdr.ipv4.dstAddr": ("10.0.0.1", 32)},
    action_name="ingress.send_to_port",
    action_params={"port": 1}
)
3. write_table_entry()
Sends a WriteRequest to the P4 switch to insert, modify, or delete a table entry.
Parameters:
table_entry: The table entry created by make_table_entry().
modify: Boolean flag. False for insert, True for modify.
Example:
python
Copy code
sh.write_table_entry(table_entry, modify=False)  # Insert a new entry
4. read_table_entries()
Reads and prints all entries from a table.
Parameters:
table_name: The name of the table to read.
Example:
python
Copy code
sh.read_table_entries("ingress.ipv4_lpm")
5. delete_table_entry()
Deletes a specific entry from a table.
Parameters:
table_entry: The table entry to delete, created by make_table_entry().
Example:
python
Copy code
sh.delete_table_entry(table_entry)
6. write_multicast_group_entry()
Adds, modifies, or deletes multicast group entries.
Parameters:
multicast_group_id: ID of the multicast group.
replicas: List of replicas (port and instance pairs).
Example:
python
Copy code
sh.write_multicast_group_entry(
    multicast_group_id=1,
    replicas=[{'egress_port': 1, 'instance': 0}, {'egress_port': 2, 'instance': 0}]
)
7. read_counters()
Reads counters (direct or indirect) and prints the result.
Parameters:
counter_name: Name of the counter (from P4Info).
index: (Optional) Index to read (for indirect counters).
Example:
python
Copy code
sh.read_counters("MyIngress.packet_counter", index=1)
8. write_meter_entry()
Writes (insert/modify) meter configurations to the switch.
Parameters:
meter_name: Name of the meter (from P4Info).
config: Meter configuration (rate and burst size).
index: Meter index.
Example:
python
Copy code
sh.write_meter_entry(
    meter_name="MyIngress.meter",
    config={"cir": 1000, "cburst": 500, "pir": 2000, "pburst": 1000},
    index=1
)
9. write_clone_session_entry()
Configures clone sessions (used for mirroring packets).
Parameters:
session_id: ID of the clone session.
egress_port: Port to which cloned packets will be sent.
packet_length_bytes: (Optional) Max length of the cloned packet.
Example:
python
Copy code
sh.write_clone_session_entry(session_id=1, egress_port=2)
10. write_group_entry()
Writes (insert/modify) action group entries.
Parameters:
group_id: Group ID.
action_profiles: List of action profiles (e.g., actions and weights).
Example:
python
Copy code
sh.write_group_entry(
    group_id=1,
    action_profiles=[{'action': 'MyIngress.action', 'weight': 1}]
)
11. read_group_entries()
Reads group entries from the switch.
Example:
python
Copy code
sh.read_group_entries()
12. read_digest_list()
Reads and prints digest list entries.
Example:
python
Copy code
sh.read_digest_list("MyIngress.digest_list")
Utility Functions:
get_device_info(): Retrieves information about the P4 device.

python
Copy code
device_info = sh.get_device_info()
master_arbitration(): Performs master arbitration to become the master of the P4Runtime session.

python
Copy code
sh.master_arbitration()
get_p4info(): Retrieves and prints the P4Info details (tables, actions, etc.).

python
Copy code
sh.get_p4info()
get_pipeline_config(): Reads and prints the current pipeline configuration.

python
Copy code
sh.get_pipeline_config()
Typical Workflow
Set Pipeline Configuration:

python
Copy code
sh.set_fwd_pipe(p4info_path="path/to/p4info.txt", bin_path="path/to/p4_program.bin")
Insert Table Entry:

python
Copy code
table_entry = sh.make_table_entry("ingress.ipv4_lpm", {"hdr.ipv4.dstAddr": ("10.0.0.1", 32)}, "ingress.send_to_port", {"port": 1})
sh.write_table_entry(table_entry, modify=False)
Read Table Entries:

python
Copy code
sh.read_table_entries("ingress.ipv4_lpm")
Delete Table Entry:

python
Copy code
sh.delete_table_entry(table_entry)
The p4runtime-shell simplifies the complex interactions required by P4Runtime, making it easier to interact with tables, meters, counters, multicast groups, and other P4 pipeline elements. Let me know if you need help with specific commands!
