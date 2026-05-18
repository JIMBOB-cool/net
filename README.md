# UDP Protocol for Distributed Neural Network

## Collaborators

- GINO SEBASTIAN DIAZ NEYRA
- RODRIGO GOMEZ SAN ROMAN
- MATIAS PAVEL SANCHEZ CUNO
- NAYALEM KARIM ARUNE CHAHUA

---

## Datagram Structure

Each UDP datagram has a fixed size of *500 bytes*.  
The protocol uses the following structure:

# Datagram Structure

```text
+------+-----+---------+-------------+-----------+-----------+----------+------+
| TYPE | SEQ | NODE_ID | CHUNK_TOTAL | CHUNK_NUM | DATA_SIZE |   DATA   | HASH |
+------+-----+---------+-------------+-----------+-----------+----------+------+
| 1 B  | 1 B |   2 B   |     4 B     |    4 B    |    4 B    | Variable | 32 B |
+------+-----+---------+-------------+-----------+-----------+----------+------+
```

---

## Packet Fields

| Field | Size | Description |
|:--|:--:|:--|
| **TYPE** | 1 byte | Identifies the datagram type. Allows the receiver to interpret the `DATA` field correctly. Example values: `MATRIX`, `DATA`, `RESULT`, `ACK`, `NACK`, `FINISH`. |
| **SEQ** | 1 byte | Datagram sequence number. Used to match packets with their corresponding `ACK` or `NACK`. |
| **NODE_ID** | 2 bytes | Logical identifier of the node associated with the datagram. Example: `M0` for master node, `S1`, `S2`, `S3` for slave nodes. |
| **CHUNK_TOTAL** | 4 bytes | Total number of chunks when the transmitted data is fragmented. |
| **CHUNK_NUM** | 4 bytes | Current chunk index. Helps reconstruct fragmented data in the correct order. |
| **DATA_SIZE** | 4 bytes | Actual size of the `DATA` field in bytes. Indicates how many bytes contain valid information. |
| **DATA** | Variable | Payload of the datagram. Can contain matrices, weights, input data, partial results, control messages, or errors. |
| **HASH** | 32 bytes | Integrity verification hash. The receiver recalculates the hash and compares it against this field to detect corruption. |

---

## Datagram Layout

```text
┌────────┬─────┬─────────┬─────────────┬───────────┬───────────┬──────────┬──────┐
│ TYPE   │ SEQ │ NODE_ID│ CHUNK_TOTAL│ CHUNK_NUM│ DATA_SIZE│   DATA   │ HASH │
├────────┼─────┼─────────┼─────────────┼───────────┼───────────┼──────────┼──────┤
│ 1 byte │ 1 B │  2 B    │    4 B      │   4 B     │    4 B    │ Variable │ 32 B │
└────────┴─────┴─────────┴─────────────┴───────────┴───────────┴──────────┴──────┘
```

---

## Notes

- The protocol supports **fragmented transmission** through `CHUNK_TOTAL` and `CHUNK_NUM`.
- `SEQ` enables reliability mechanisms such as acknowledgments (`ACK`) and retransmissions (`NACK`).
- `HASH` provides **datagram integrity verification**.
- `DATA_SIZE` allows variable-length payloads inside the datagram.
