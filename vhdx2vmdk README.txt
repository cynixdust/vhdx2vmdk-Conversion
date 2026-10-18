VHDX <-> VMDK Converter
=======================

Developed by Gabriel Charles Bannerman-Hyde / 4linksisc labs

OVERVIEW
--------
This tool converts between Microsoft VHDX and VMware VMDK virtual disk images,
in either direction:

    * VHDX -> VMDK  (Microsoft Hyper-V / Windows to VMware)
    * VMDK -> VHDX  (VMware to Microsoft Hyper-V / Windows)

It can be used from the graphical interface, from the command line, or as the
prebuilt standalone executables. No third-party software is required.

QUICK START
-----------
1. Choose the Direction (VHDX -> VMDK or VMDK -> VHDX).
2. Click "File > Open source..." (or the "Browse..." button) and choose the
   source disk file.
3. Choose where to save the output with "File > Save destination as..." (or
   the "Save as..." button).
4. Adjust any options if needed (see below). The defaults are fine for most
   users.
5. Click "Convert" and wait for the progress bar to finish.
6. Click "Open output folder" to view the result.

DIRECTION
---------
VHDX -> VMDK
    Reads a Microsoft VHDX image and writes a VMware VMDK image. The Format,
    Adapter type, Hardware version, Content ID and Parent VHDX options apply.

VMDK -> VHDX
    Reads a VMware VMDK image (single-file stream-optimized or a
    descriptor + flat pair) and writes a Microsoft VHDX dynamic disk. The
    VHDX block size option applies.

FIELDS AND OPTIONS
------------------

Source
    The input disk image. For VHDX -> VMDK this is a .vhdx file. For
    VMDK -> VHDX this is a .vmdk file (either a single self-contained
    stream-optimized disk, or a text descriptor that references a -flat.vmdk
    data file).

Destination
    The output disk file to create. Its extension determines the direction
    automatically when using the command line (.vhdx or .vmdk).

Format (--subformat)   [VHDX -> VMDK only]
    The layout of the VMDK file that is written. Two subformats are supported:

    * streamOptimized (default)
        A single, compressed, sparse disk. Small output files with minimal
        disk usage; ideal for uploading to cloud providers, OVF/OVA packages,
        or archiving. The file grows only with the data that is actually
        stored.

    * monolithicFlat
        An uncompressed disk stored alongside a small text descriptor. This
        produces two files: a tiny ".vmdk" descriptor (text) and a
        "-flat.vmdk" file that contains the raw disk data. Use this when you
        need a plain disk image, for example to mount or inspect its contents.

Adapter type (--adapter-type)   [VHDX -> VMDK only]
    The controller type recorded in the VMDK descriptor (ddb.adapterType).
    This tells the hypervisor which virtual controller to emulate. Options:

    * ide        (default) - IDE controller. Widely compatible.
    * lsilogic             - LSI Logic SCSI controller. Common for Windows
                             guests.
    * buslogic             - BusLogic SCSI controller. Common for older
                             Windows guests.
    * legacyesx            - Legacy ESX controller.

    This value also affects the disk geometry (number of heads) recorded in
    the descriptor and should match the controller used by the virtual
    machine.

Hardware version (--hw-version)   [VHDX -> VMDK only]
    The virtual hardware version recorded in the descriptor
    (ddb.virtualHWVersion). The default is "4", which is widely compatible.
    Higher values may be required for newer VMware features.

Content ID / CID (--cid)   [VHDX -> VMDK only]
    A 32-bit hexadecimal identifier for the disk. If left blank, a random
    value is generated. Use a fixed value only if you need to match an
    existing disk chain.

Parent VHDX (--parent)   [VHDX -> VMDK only]
    An optional parent VHDX file. Use this when converting a differencing
    (child) disk that depends on a parent image, so the parent can be read
    while the child is converted. Leave empty for a normal, base disk.

VHDX block size (--vhdx-block-size)   [VMDK -> VHDX only]
    The allocation block size of the VHDX file that is written. The default is
    1 MiB. Larger values (up to 32 MiB) reduce the number of metadata entries
    for very large disks but increase the minimum amount of space used by a
    partially filled block. Most users should keep the default.

MENUS
-----
File     Open source, Save destination as, Convert, Open output folder, Exit.
View     Direction, Reset fields, Always on top, Show usage / README.
Options  Quick selection of the Format and Adapter type (VHDX -> VMDK).
Help     Usage / README, Download README.txt, About.

COMMAND LINE USAGE
------------------
The direction is chosen automatically from the destination extension.

    vhdx2vmdk disk.vhdx disk.vmdk
    vhdx2vmdk disk.vhdx disk.vmdk --subformat monolithicFlat
    vhdx2vmdk disk.vhdx disk.vmdk --adapter-type lsilogic
    vhdx2vmdk disk.vmdk disk.vhdx
    vhdx2vmdk disk.vmdk disk.vhdx --vhdx-block-size 2097152
    vhdx2vmdk --info disk.vhdx
    vhdx2vmdk --info disk.vmdk

    --subformat {streamOptimized,monolithicFlat}   VMDK output layout
    --adapter-type {buslogic,ide,legacyesx,lsilogic}
    --hw-version VERSION
    --cid HEX
    --parent PATH
    --vhdx-block-size BYTES                       VHDX output block size
    --info                                        print image info and exit
    --version                                     show version

TROUBLESHOOTING
---------------
* "Source file not found": check that the source path exists and points to a
  .vhdx or .vmdk file.
* Conversion fails on a differencing disk: provide the matching parent VHDX
  in the "Parent VHDX" field.
* A VMDK descriptor references a -flat.vmdk data file: keep the descriptor and
  its data file together in the same folder when converting back to VHDX.
* A running copy locks the executable: close it before rebuilding.
* Explorer shows a stale icon: run "ie4uinit.exe -show" or sign out and in.

LICENSE
-------
MIT

Developed by Gabriel Charles Bannerman-Hyde / 4linksisc labs
