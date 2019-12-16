# Subvolume create/delete/clean

## Where?
* create: create_subvol
* delete: btrfs_ioctl_snap_destroy
* clean: btrfs_clean_one_deleted_snapshot

## What?

### BTRFS_FSID
The metadata_uuid of the filesystem where the subvolume was created/deleted

### BTRFS_OBJECTID
The subvolume objectid that is being created/deletes

### BTRFS_PARENT_OBJECTID
The objectid of the parent node of the subvolume being created/deleted

### BTRFS_SUBVOLUME_NAME
The name of the subvolume being created/deleted

### BTRFS_SUBVOLUME_CREATE=1
Exported only when a subvolume is being created

### BTRFS_SUBVOLUME_DELETE=1
Exported only when a subvolume is being deleted

### BTRFS_SUBVOLUME_CLEAN=1
Exported only when a subvolume is being deleted (removed from the subvolume btree)