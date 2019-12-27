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

# Scrub start/stop

## Where?
* start: btrfs_scrub_dev (before scrub_supers)
* stop: btrfs_scrub_dev (after the progress memcpy)

## What?

### BTRFS_FSID
The metadata_uuid of the filesystem where scrub started

### BTRFS_DEVID
Device ID of the device which scrub is running

### BTRFS_SCRUB_START=1
Exported when the scrub procedure started

### BTRFS_SCRUB_STOP=1
Exported when the scrub procedure finishes

### BTRFS_SCRUB_RESULT
Will contain the one of the following values:

* 0 (succeed)
* 1 (scrub couldn't be performed)
* 2 (there is nothing to resume)
* 3 (scrub found uncorrectable errors)

# Balance start/stop

## Where?
* start: btrfs_balance (before calling \__btrfs_balance)
* stop: btrfs_balance (after calling \__btrfs_balance)

## What?

### BTRFS_FSID
The metadata_uuid of the filesystem being balanced

### BTRFS_DEVID
The device which is being executing the balance

### BTRFS_BALANCE_DATA=1
Exported when the balance is acting on DATA blocks

### BTRFS_BALANCE_METADATA=1
Exported when the balance is acting on METADATA blocks

### BTRFS_BALANCE_SYSTEM=1
Exported when the balance is acting on SYSTEM blocks

### BTRFS_BALANCE_RESULT
Will contain one of the following values:

* 0 (succeed)
* 1 (error?)
* 12 (ENOMEM)
* 28 (ENOSPC)
* 125 (ECANCELED)
* ...

# Filesystem size changed

## Where?
* btrfs_ioctl_resize

## What?

### BTRFS_FSID
The metadata_uuid of the filesystem being resized

### BTRFS_DEVID
The device ID of the resized device

### BTRFS_OLD_SIZE
Contains the device size before the resize

### BTRFS_NEW_SIZE
Contains the device size after the resize
