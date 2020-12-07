## ATA Secure Erase

Find the SSD you want to erase.

>sudo blkid

```
/dev/sda1: LABEL="Recovery" UUID="90209FB4209FA030" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="b5f18159-880e-4957-9351-89b071f7a408"
/dev/sda2: UUID="429F-D815" TYPE="vfat" PARTLABEL="EFI system partition" PARTUUID="e2ea2fdd-0519-4b34-be60-84eba5f0891a"
/dev/sda4: UUID="4A9AA3879AA36DDF" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="3bddf065-bed4-48e1-bb08-9c77fca81d87"
/dev/sdb1: LABEL="UBUNTU 20_0" UUID="540F-D17D" TYPE="vfat" PARTUUID="28d3876e-01"
/dev/loop0: TYPE="squashfs"
/dev/loop1: TYPE="squashfs"
/dev/loop2: TYPE="squashfs"
/dev/loop3: TYPE="squashfs"
/dev/loop4: TYPE="squashfs"
/dev/loop5: TYPE="squashfs"
/dev/sda3: PARTLABEL="Microsoft reserved partition" PARTUUID="23b22f34-d8a4-451d-82e3-3a3208f14f76"
```

>sudo hdparm -I /dev/sd**X**

```
Security: 
	Master password revision code = 65534
		supported
	not	enabled
	not	locked
		frozen
	not	expired: security count
		supported: enhanced erase
	2min for SECURITY ERASE UNIT. 2min for ENHANCED SECURITY ERASE UNIT.
Device Sleep:
	DEVSLP Exit Timeout (DETO): 40 ms (drive)
	Minimum DEVSLP Assertion Time (MDAT): 31 ms (drive)
Checksum: correct
```

Command output should say *not frozen*. If it says *frozen* *(like in my example)*, run the following command that will suspend the OS.
When the OS is suspended, move your mouse / use keyboard to wake it up.

>sudo bash -c 'echo mem >/sys/power/state'

>sudo hdparm -I /dev/sd**X**

Now the command output should say *not frozen*.

```
Security: 
	Master password revision code = 65534
		supported
	not	enabled
	not	locked
	not     frozen
	not	expired: security count
		supported: enhanced erase
	2min for SECURITY ERASE UNIT. 2min for ENHANCED SECURITY ERASE UNIT.
Device Sleep:
	DEVSLP Exit Timeout (DETO): 40 ms (drive)
	Minimum DEVSLP Assertion Time (MDAT): 31 ms (drive)
Checksum: correct
```

Run the following command that will set the temporary password you want. After the secure erase the password will be set back to NULL.

>sudo hdparm --user-master u --security-set-pass **Password** /dev/sd**X**

```
security_password: "Password"
/dev/sda:
 Issuing SECURITY_SET_PASS command, password="Password", user=user, mode=high
```

>sudo hdparm -I /dev/sd**X**

Command output should say *enabled*.

```
Security: 
	Master password revision code = 65534
		supported
		enabled
	not	locked
	not	frozen
	not	expired: security count
		supported: enhanced erase
	Security level high
	2min for SECURITY ERASE UNIT. 2min for ENHANCED SECURITY ERASE UNIT.
Device Sleep:
	DEVSLP Exit Timeout (DETO): 40 ms (drive)
	Minimum DEVSLP Assertion Time (MDAT): 31 ms (drive)
Checksum: correct
```

Run the following command that will erase the SSD. Remember to use the temporary password you set.

>sudo hdparm --user-master u --security-erase **Password** /dev/sd**X**

```
security_password: "Password"
/dev/sda:
 Issuing SECURITY_ERASE command, password="Password", user=user
```

>sudo hdparm -I /dev/sd**X**

Command output should say *not enabled*.

```
Security: 
	Master password revision code = 65534
		supported
	not	enabled
	not	locked
	not	frozen
	not	expired: security count
		supported: enhanced erase
	2min for SECURITY ERASE UNIT. 2min for ENHANCED SECURITY ERASE UNIT.
Device Sleep:
	DEVSLP Exit Timeout (DETO): 40 ms (drive)
	Minimum DEVSLP Assertion Time (MDAT): 31 ms (drive)
Checksum: correct
```

If you run the following command, the SSD shouldn't be showing in the command output anymore.

>sudo blkid

```
/dev/sdb1: LABEL="UBUNTU 20_0" UUID="540F-D17D" TYPE="vfat" PARTUUID="28d3876e-01"
/dev/loop0: TYPE="squashfs"
/dev/loop1: TYPE="squashfs"
/dev/loop2: TYPE="squashfs"
/dev/loop3: TYPE="squashfs"
/dev/loop4: TYPE="squashfs"
/dev/loop5: TYPE="squashfs
```

Now the SSD is successfully erased.
