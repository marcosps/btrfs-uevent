Use 80-local.rules for simple testing/demonstration.

`# cp 80-local.rules /etc/udev/rules.d`  
`# udevadm control --reload`

# TODO

BTRFS_FSID should probably be generic.


# Subvolume create/delete/clean

## Where?
* create: create_subvol
* delete: btrfs_ioctl_snap_destroy
* clean: btrfs_clean_one_deleted_snapshot

## What?

### BTRFS_FSID
The metadata_uuid of the filesystem where the subvolume was created/deleted

### BTRFS_SUBVOLUME_OBJECTID
The subvolume objectid that is being created/deletes

### BTRFS_SUBVOLUME_PARENT_OBJECTID
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

# Checksum mismatch detected

## Where?
* io_ctl_check_crc -- need further checking if we can/should send an uevent from here
* check_compressed_csum
* end_compressed_bio_read -- check_compressed_csum() is called here, and we can't emit an incomplete uevent for the assumed case of a failed IO in the cb
* \_\_readpage_endio_check (call sites?)
* btrfs_print_data_csum_error (?) -- shouldn't send uevent from here
* end_bio_extent_readpage -- not sure csum is checked here and the below functions
  * bio_readpage_error
    * Check if btrfs_check_repairable fails (?)

## What?

Note we identify as "CSUM" to keep it short, but it's only for mismatch cases.

### BTRFS_FSID
The metadata_uuid of the filesystem when the csum mismatch happened

### BTRFS_CSUM_EXPECTED_CSUM
Expected checksum value (as the one stored in btree).

### BTRFS_CSUM_COMPUTED_CSUM
Actual checksum value computed.

### BTRFS_CSUM_ROOT_OBJID
Root objectid where the checksum mismatch happened

### BTRFS_CSUM_INODE
inode with mismatched checksum

### BTRFS_CSUM_OFFSET
Logical start.

### BTRFS_CSUM_MIRROR_NUM
Number of available copies of the data

XXX: Should we report "where" does the csum fail? e.g. compressed bio, free-space-cache, etc

# Checksum mismatch detected, and repaired

## Where?
* repair_io_failure (?)

# What?

### BTRFS_FSID
The metadata_uuid of the filesystem when the csum mismatch happened

### BTRFS_DEVID
The device ID where the csum mismatch was repaired

### BTRFS_SECTOR_REPAIRED
The sector number that was fixed

# Device added/deleted/replace

## Where

* btrfs_ioctl_add_dev()
* btrfs_ioctl_rm_dev_v2()
* btrfs_ioctl_rm_dev()
* btrfs_dev_replace_finishing() (watch for scrub here too)
* btrfs_dev_replace_cancel()
* btrfs_dev_replace_start()
* ...

## What?

### BTRFS_FSID
UUID of filesystem which got modified

### BTRFS_DEVICE_{ADDED,DELETED,REPLACE}_PATH
Path to the device being added/deleted/replaced

### BTRFS_DEVICE_REPLACE_PATH_OLD
Path to device being replaced

### BTRFS_DEVICE_REPLACE_PATH_NEW
Path to device replacing BTRFS_DEVICE_REPLACE_PATH_OLD

### BTRFS_DEVICE_REPLACE_STATE
Current state of the replace command. Possible values are: started, canceled,
suspended, finished, and unknown.

### BTRFS_DEVICE_REPLACE_SRC_DEVID
devid of the device being replaced.

### BTRFS_DEVICE_REPLACE_PROGRESS
Progress (INT) of the replace operation.
