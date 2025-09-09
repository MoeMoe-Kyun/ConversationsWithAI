Great question — you're talking about **nouns that express a stronger, more intense, or more extreme version of another noun** describing the same basic category of person or thing.

There isn’t a widely known single term for this kind of relationship in standard grammar, but we can describe it accurately using a few concepts.

**Most precise description:**
→ **"Intensified synonym"** or **"intensified noun"**
This isn't an official grammatical term, but it's descriptive.

**Related linguistic concepts:**

1. **Hypernym & Hyponym** (not a perfect fit):

   * *redacted* is a **hyponym** of *redacted* (more specific kind).
   * *redacted* is a **hypernym** of *redacted* (broader term).
   * But these don’t imply intensity — they imply specificity.

2. **Register / Connotation / Intensity:**

   * *redacted* carries stronger **connotation** and a more **informal or pejorative register**.
   * It conveys **greater intensity**, **severity**, or **extremity**.
   * So you could say *redacted* is a "**colloquial, intensified form of redacted**."

3. **Pejorative Intensifier:**

   * In cases where the stronger noun is also more negative (as in *redacted* vs. *redacted*), you might call it a **pejorative intensifier**.

---

**Would it delete external drives? → Yes, it can.**

If a drive is mounted at `/mnt/external`, then `rm -rf /` will enter that mount point and begin deleting files **on the external drive** as well — assuming:

* You have permission (e.g., root).
* The drive is mounted **read-write**.
* There are no mount protections (e.g., `noexec`, `ro`).

**Modern protections include:**

1. `--preserve-root` is default on many systems (prevents `rm -rf /`).
2. Permissions and mount flags.
3. Some filesystems may survive via snapshots (see next discussion).

---

You're absolutely on point — let's dive into how **snapshots in Btrfs and ZFS** work, where they live, and how they’re managed.

---

### What is a snapshot?

A **snapshot** is a **point-in-time, read-only (or read-write) version of a filesystem or subvolume**.
They’re efficient: no copying, just marking data blocks and tracking changes.

---

### Where are snapshots stored?

> **Inside the same filesystem**, not on a separate partition (unless explicitly configured that way).

So if you're using Btrfs or ZFS on `/dev/sda1`, the data, snapshots, and metadata all live **within that same space**.

---

### Btrfs:

* Based on **subvolumes**.
* Snapshots are **special subvolumes**.
* You can mount/access them like regular dirs.
* Often stored in places like `/.snapshots/` or `/home/.snapshots/`.

**Do they grow?**
Yes — indirectly. As data changes, old blocks are preserved, increasing disk use.

**Cleaning mechanism?**
Yes — via tools like:

* **Snapper**
* **Timeshift**
* Custom scripts

These apply retention rules like “keep daily for 7 days, weekly for 4 weeks,” etc.

---

### ZFS:

* Snapshots are **internal references**, not in the normal directory tree unless exposed.
* You can access them via `.zfs/snapshot/`, e.g.:

  ```
  /home/user/.zfs/snapshot/snap-2023-09-09/
  ```
* These are often **invisible unless enabled**.
* `rm -rf /` would not typically affect them unless that `.zfs` dir is visible and traversed.

**Do they grow?**
Yes, due to preserved data blocks after snapshot.

**Cleaning?**
Yes — tools like `zfs-auto-snapshot` clean up old ones via policies.

---

### Can snapshots survive `rm -rf /`?

**Depends:**

* Btrfs snapshots under `/` could be deleted.
* ZFS snapshots are safer if not mounted or visible.
* Snapshots don’t help if the **entire pool or volume is destroyed**.

---

### Can snapshots be on another partition?

Not exactly — but you can:

* Create subvolumes/datasets on another device.
* Use `btrfs send` or `zfs send` to replicate snapshots externally.
* Use backup strategies to store snapshots on separate physical storage.

Let me know if you want this saved in Markdown, PDF, or another format!
