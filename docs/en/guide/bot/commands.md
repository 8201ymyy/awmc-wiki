# maimai Bot (koishi-plugin-maibot) Command Reference


## 1. Before You Start

- **Command Prefix**: All examples below use `/` as the prefix, consistent with Koishi command prefix configuration.


## 2. Store & "Priority Authorization"

### 2.1 Default Purchase Link (Store)

The plugin uses the following link by default in **cooldown prompts**, **unbind card guides**, and similar scenarios (configurable):

- **Priority Authorization / General Store**: https://store.awmc.cc/


### 2.2 What is "Priority Authorization"?

When **`priorityCooldown`** is enabled, regular users will enter a **cooldown** period for certain features; the `{shopUrl}` in prompt text refers to the store URL above.

- **Personal Priority**: After redeeming a **personal key**, the corresponding account enjoys shorter or zero cooldown **globally** (as configured).
- **Group Priority**: After redeeming a **group key** (must be redeemed **within the target group**); once active, **all group members within that group** are exempt from cooldown when using the Bot (does not apply in private messages).
- **Admin Bypass**: When a user's `authority` is **greater than** `adminBypassAuthority` (default **4**), they **bypass cooldown** and may automatically sync a permanent personal priority entry (cleared when authority drops; **unrelated to key redemption records**).




## 3. How to Redeem Keys

### 3.1 Command

```
/mai redeem key <key>
```

Or send `/mai redeem key` first, then paste the full key within the **time limit** prompted by the Bot (usually starts with **MAI-**).

### 3.2 Three Types of Keys

| Type | Redemption Environment | Description |
|------|----------------------|-------------|
| **Personal** | Private message or group | The bound account enjoys priority cooldown **globally**. |
| **Group** | **Must be redeemed within the target group** | Bound to the current group; all group members enjoy no cooldown **within the group**. |
| **Unbind** | Private message or group | Must have already executed `/mai bind`; after redemption, adds **unbind quota** to the current binding for `/mai unbind key` (alias: `maiunbindkey`) during cooldown. |

### 3.3 Group Priority Follow-up Actions (Redeemer)

- **`/mai cancel group priority`**: Cancel group priority for the current group (only the **group key redeemer** and their cross-platform linked accounts can operate).
- **`/mai transfer group priority`**: Initiate migration in the **original group**.
- **`/mai receive group priority`**: Complete the transfer in the **target group** (used with the above command).

If historical data has no redeemer record, an admin can use **`/mai admin cancel group priority`** to handle it.

---

## 4. Help & Aliases

| Command | Description |
|---------|-------------|
| `/mai` / `/mai help` | View built-in help; add `--advanced` to expand ticket, collectible, and travel distance features. |
| `/mai status` | Query binding and priority authorization status. |
| `/mymai` | Same as `/mai status`. |
| `/mai unbind key` | `maiunbindkey` is an alias. |
| `/mai unlock` / `/mai escape` | Only effective when lock-related commands are re-registered in source code; currently disabled in default builds. |

---

## 5. Regular User Commands (Non-Admin)

### 5.1 Account & Connection

| Command | Description |
|---------|-------------|
| `/mai bind [QR code or link]` | Bind maimai DX account (SGID text or official account webpage, etc.). |
| `/mai unbind` | Unbind maimai DX (Bot will prompt about unbind cards when rebind cooldown is active). |
| `/mai unbind key` | Unbind using **unbind key quota** during cooldown (requires SGID verification and confirmation). |
| `/mai status [target]` | Check your own status; high authority can check others. |
| `/maiping` | Test arcade connection. |
| `/mai预览` / `/预览` | Query the account preview; costs 5 BREAK on success. |
| `/mai道具` / `/道具` | Query owned items; costs 5 BREAK on success. |
| `/mai门状态` / `/查门` / `/门状态` | Query Kaleidx Gate discovery, key, and clear status; costs 5 BREAK on success. |

Gate status includes the names: 1 Blue Gate, 2 White Gate, 3 Purple Gate, 4 Black Gate,
5 Yellow Gate, 6 Red Gate, 7 Prism Tower, 8 Outer Gate, 9 Gate of Hope, and 10 Inner Gate.

### 5.2 Diving Fish B50

| Command | Description |
|---------|-------------|
| `/mai bind fish <token> [target]` | Bind Diving Fish Token. |
| `/mai unbind fish [target]` | Unbind Diving Fish Token; maimai binding is preserved. |
| `/mai upload B50 [QR code or target]` | Upload B50 to Diving Fish. |
| `/maiua [QR code or Lxns code] [target]` | Upload B50 to both Diving Fish and Lxns simultaneously (SGID can go through one flow). |

### 5.3 Lxns B50

| Command | Description |
|---------|-------------|
| `/mai bind lxns <code> [target]` | Bind Lxns code. |
| `/mai unbind lxns [target]` | Unbind Lxns code. |
| `/mai upload lxns b50 [code] [target]` | Upload B50 to Lxns. |

### 5.4 Tickets, Travel Distance, Collectibles, Version & Scores (`/mai --advanced` also has a summary)

| Command | Description |
|---------|-------------|
| `/mai ticket [multiplier] [target]` | Issue a function ticket (2x or 3x; default 2x). |
| `/mai change version [QR code or target]` | Change game version number (supports caching). |
| `/mai get collectibles [SGID or @user]` | Interactively get collectibles. |
| `/mai upload song score [@user]` | Interactively upload a single song score. |
| `/mai delete score [@user]` | Interactively delete a single song score. |

#### QueryBot AWMC Gateway v2 Commands

| Command | Description |
|---------|-------------|
| `mai改成绩` / `改分 [song difficulty achievement dxScore FC FS mode]` | Interactive or one-line score edit; 75 BREAK per successful item. |
| `mai删成绩` / `删分 [song difficulty]` | Interactive or one-line score deletion; 50 BREAK per successful item. |
| `mai清票` / `清票` | Clears all Charge tickets after confirmation; 10 BREAK on success. |
| `mai改道具` / `改道具 [itemKind itemId add/del]` | Untested high-risk item mutation; 100 BREAK on success and always requires risk confirmation. |

Score editing resolves song IDs, titles, and aliases. Interactive difficulty selection accepts
`0 BASIC`, `1 ADVANCED`, `2 EXPERT`, `3 MASTER`, and `4 Re:MASTER`; songs without a Re:MASTER
chart only accept `0-3`. Achievement accepts percentages such as
`100.5%`. DX values from 0 to 5 select the default simple/fuzzy mode (DX star rating); larger
values select professional/exact mode and are validated against the chart maximum.

All commands that use account QR credentials check the shared SGID cache lifetime. When the
cache expires, the bot refuses to call the API with stale credentials and asks for a fresh
SGWCMAID, official QR link, or QR image before the command is run again.

::: danger Untested item mutation
`mai改道具` has not been tested on a real account and may cause irreversible data corruption,
especially for `itemKind` 4 or 8. The user must explicitly accept the risk before execution.
The bulk `upsert-all` endpoint is not exposed as a Bot command.
:::

#### `/mai get collectibles` Interactive Menu

After sending the command, the Bot will prompt you to select a collectible type:

| Number | Type |
|--------|------|
| 1 | Nameplate |
| 2 | Title |
| 3 | Avatar |
| 4 | Gift |
| 5 | Song |
| 6 | Unlock Master |
| 7 | Unlock Re:Master |
| 8 | Unlock Black Chart (not yet implemented) |
| 9 | Travel Partner |
| 10 | Partner |
| 11 | Background |
| 12 | Function Ticket |
| 13 | Intimacy Gift |

Enter the corresponding number (1-13), or enter `00` to cancel.

For song-related types, the Bot will further prompt for the Song ID.

### 5.5 Rank Course

The current default course table is **CN PRiSM PLUS**. The output image includes LIFE rules, the four course tracks, song level/constant, your personal best achievement, and anonymous server-side sample statistics.

| Command | Description |
|---------|-------------|
| `/段位表` | Show supported rank names and examples. |
| `/段位表 <rank>` | Query a rank course, e.g. `/段位表 真二段`. |
| `/<rank>段位表` | Reverse form, e.g. `/真二段段位表`. |
| `/段位表 <rank> @user` | Render the target user's personal score state when available. |

Rank resources prefer the player's current nameplate and official dani Plate. Anonymous samples come from recent server-side full-score caches for users who have not opted out of data sharing; low-sample or cold-start charts fall back to bundled samples. The footer identifies the data source and notes that arcade results remain authoritative.

### 5.6 Others

| Command | Description |
|---------|-------------|
| `/mai query opt <titleVer>` | Query Mai2 option file download URL. |
| `/mai redeem key [key]` | See Section 3. |
| `/mai cancel group priority` | See Section 3. |
| `/mai transfer group priority` / `/mai receive group priority` | See Section 3. |


---

## 6. Admin Commands

### 6.1 Permission Overview

| Type | Config / Condition | Default | Description |
|------|-------------------|---------|-------------|
| Key & priority management, `maibypass` | `authLevelForCardAdmin` | **4** | Generate/delete/export keys, directly modify personal/group priority, `/maibypass`, etc. |
| Cooldown bypass + auto permanent personal priority | `authority` **>** `adminBypassAuthority` | Threshold default **4** | Not a standalone command; handled by the plugin in cooldown logic. |


Optional **`-bypass`** commands are documented within the plugin (used to skip confirmations).

### 6.2 Requires `authority` >= `authLevelForCardAdmin` (default 4)

| Command | Description |
|---------|-------------|
| `/mai admin generate key [duration] [quantity]` | No params for interactive mode; supports **`-g`** group key, **`-u`** unbind key. Quick examples: `/mai admin generate key 7d 5`, `-g 30d 3`, `-u -1 10`. |
| `/mai admin delete key [key]` | Invalidate a key; supports multi-line or exported TSV batch; no params for interactive mode. |
| `/mai admin export keys [scope]` | Export as tab-separated text; quick: `all` / `unused` / `redeemed`; no params for interactive filtering. |
| `/mai admin cancel group priority [group ID]` | Cancel group priority for specified or current group. |
| `/mai admin cancel personal priority <target>` | Clear target user's personal priority record. |
| `/mai admin set personal priority <target> <spec>` | `spec` examples: `permanent`, `7d`, `clear`, etc. |
| `/mai admin set group priority <spec> [-g group ID]` | Directly set group priority; `-g` can be omitted when in group. |
| `/maibypass <target>` | Clear all command cooldowns for target user (alias: `/mai admin clear cooldown`). |

---

## 7. Community

- **Feedback QQ Group**: **1072033605**
- **Official Website**: https://awmc.cc

---
