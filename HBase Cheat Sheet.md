# 🐘 HBase Cheat Sheet

A comprehensive guide for Senior Data Engineers to master Apache HBase operations, from shell basics to advanced region management.

---

## 🔍 HBase Concepts at a Glance

| Concept | Description |
| :--- | :--- |
| **RowKey** | Unique identifier for a row. Data is lexicographically sorted by RowKey. |
| **Column Family (CF)** | Logical grouping of columns. All members of a CF are stored together in the same file. |
| **Column Qualifier** | Specific column within a CF (e.g., `info:name`, `info:age`). |
| **Cell** | Intersection of RowKey, Column Family, and Column Qualifier. |
| **Timestamp** | Versioning for each cell. Default is current system time. |

### 📊 HBase Data Model vs. Relational (SQL)

| Feature | Relational (SQL) | HBase (NoSQL) |
| :--- | :--- | :--- |
| **Storage** | Row-oriented | Column-family oriented |
| **Schema** | Fixed (Strict) | Schema-less (Dynamic columns) |
| **Transactions** | Multi-row (ACID) | Row-level only |
| **Scaling** | Vertical (Mostly) | Horizontal (Linear) |
| **Null values** | Stored as NULL | Not stored (Sparse) |

---

## 🚀 HBase Shell Basics

| Command | Effect |
| :--- | :--- |
| `hbase shell` | Access the HBase interactive shell |
| `status` | View cluster status (Servers, regions, load) |
| `version` | Display HBase version |
| `whoami` | Show current HBase user |
| `list_namespace` | List all namespaces in the cluster |
| `help` | Show all shell commands |
| `exit` | Quit the HBase shell |

---

## 📂 Table Management (DDL)

> [!IMPORTANT]
> In HBase, most schema changes (`alter`, `drop`) require the table to be **disabled** first.

| Command | Effect |
| :--- | :--- |
| `create 'tbl', 'cf1', 'cf2'` | Create a table with multiple Column Families |
| `list` | List all tables in the current namespace |
| `describe 'tbl'` | View table structure and CF configurations |
| `disable 'tbl'` | Disable a table (Stops all I/O) |
| `enable 'tbl'` | Re-enable a disabled table |
| `is_enabled 'tbl'` | Check if the table is currently active |
| `exists 'tbl'` | Check if a table exists |
| `alter 'tbl', {NAME => 'cf1', VERSIONS => 3}` | Update CF properties (e.g., Max versions) |
| `alter 'tbl', NAME => 'cf2', METHOD => 'delete'` | Remove a Column Family |
| `drop 'tbl'` | Delete a table (Table must be **disabled** first) |
| `truncate 'tbl'` | Disable, drop, and recreate a table (Clears all data) |

---

## 📥 Data Operations (DML)

| Command | Effect |
| :--- | :--- |
| `put 'tbl', 'row1', 'cf:col', 'val'` | Insert or update a single cell |
| `get 'tbl', 'row1'` | Retrieve all columns for a specific RowKey |
| `get 'tbl', 'row1', {COLUMN => 'cf:col'}` | Get a specific column value |
| `get 'tbl', 'row1', {TIMERANGE => [ts1, ts2]}` | Retrieve data within a specific timestamp range |
| `scan 'tbl'` | Scan all rows in the table (⚠️ Heavy operation) |
| `scan 'tbl', {LIMIT => 10}` | Scan with a limit on result count |
| `scan 'tbl', {STARTROW => 'a', STOPROW => 'z'}` | Range scan (Lexicographical) |
| `count 'tbl'` | Count total rows (Very slow for large tables) |
| `delete 'tbl', 'row1', 'cf:col'` | Delete a specific cell version |
| `deleteall 'tbl', 'row1'` | Delete all cells in a specific row |

---

## 🔐 Permissions & Security

| Command | Effect |
| :--- | :--- |
| `grant 'user', 'RWXCA', 'tbl'` | Grant permissions (Read, Write, eXec, Create, Admin) |
| `revoke 'user', 'tbl'` | Revoke user permissions on a table |
| `user_permission 'tbl'` | List all permissions for a specific table |

---

## ⚙️ Advanced Features

### 📸 Snapshots
| Command | Effect |
| :--- | :--- |
| `snapshot 'tbl', 'snap_name'` | Create a point-in-time snapshot |
| `list_snapshots` | List all available snapshots |
| `restore_snapshot 'snap_name'` | Restore a table from a snapshot |
| `delete_snapshot 'snap_name'` | Delete a snapshot |

### 🗺️ Region Management
| Command | Effect |
| :--- | :--- |
| `split 'tbl', 'row_split_key'` | Manually split a region |
| `major_compact 'tbl'` | Trigger a Major Compaction (Merges HFiles) |
| `compact 'tbl'` | Trigger a Minor Compaction |
| `balance_switch true/false` | Enable or disable the Region Balancer |
| `move 'encoded_region_id', 'target_server'` | Manually move a region to another server |

### 🧪 Filtering (Basic Scan Examples)
| Filter Type | Scan Example |
| :--- | :--- |
| **PrefixFilter** | `scan 'tbl', {FILTER => "PrefixFilter('user_')"}` |
| **ValueFilter** | `scan 'tbl', {FILTER => "ValueFilter(=, 'binary:active')"}` |
| **SingleColumn** | `scan 'tbl', {FILTER => "SingleColumnValueFilter('cf', 'col', =, 'binary:val')"}` |

---

## 🩺 Troubleshooting & Performance

| Tool / Check | Action |
| :--- | :--- |
| **RegionServer Health** | Check Master UI (Default port: 16010) |
| **Slow Logs** | Check `hbase-*.log` on RegionServers |
| **Zookeeper** | `zk_dump` in HBase shell to check ZK state |
| **Consistency** | `hbck` command (Run from OS shell: `hbase hbck`) |

---

## 💡 Best Practices (Data Engineer Insights)

1.  **RowKey Design**: Avoid sequential keys (e.g., timestamps). Salting or hashing RowKeys prevents **Hotspotting** on a single RegionServer.
2.  **Column Families**: Keep the number of CFs low (usually 1-3). Large numbers of CFs hurt compaction performance.
3.  **Disable before DDL**: Always `disable` your table before `alter` or `drop`.
4.  **Scan with Care**: Always use `STARTROW` and `STOPROW` for scans in production to avoid full table scans.
5.  **Pre-splitting**: For high-volume tables, pre-split regions during table creation to distribute load immediately.
    *   *Example*: `create 'tbl', 'cf', {SPLITS => ['a', 'b', 'c']}`