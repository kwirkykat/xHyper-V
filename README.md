[![Build status](https://ci.appveyor.com/api/projects/status/tsdbv0hgrxvmbo5y/branch/master?svg=true)](https://ci.appveyor.com/project/PowerShell/xhyper-v/branch/master)

# xHyper-V

The **xHyper-V** DSC module configures and manages a Hyper-V host using the **xVhd**, **xVMHyperV**, **xVMSwitch**, **xVhdFileDirectory** resources. 

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Contributing
Please check out common DSC Resources [contributing guidelines](https://github.com/PowerShell/DscResource.Kit/blob/master/CONTRIBUTING.md).


## Resources

* **xVhd** manages VHDs in a Hyper-V host. 
* **xVMHyperV** manages VMs in a Hyper-V host.
* **xVMSwitch** manages virtual switches in a Hyper-V host. 
* **xVhdFileDirectory** manages files or directories in a VHD. 
You can use it to copy files/folders to the VHD, remove files/folders from a VHD, and change attributes of a file in a VHD (e.g. change a file attribute to 'ReadOnly' or 'Hidden').
This resource is particularly useful when bootstrapping DSC Configurations into a VM. 

### xVhd

* **Name**: The desired VHD file name 
* **Path**: The desired Path where the VHD will be created
* **ParentPath**: Parent VHD file path, for differencing disk 
* **MaximumSizeBytes**: Maximum size of VHD to be created 
* **Generation**: Virtual disk format: { Vhd | VHDx }
* **Ensure**: Ensures that the VHD is Present or Absent 

### xVMHyperV

* **Name**: The desired VM name 
* **VhdPath**: The desired VHD associated with the VM 
* **SwitchName**: Virtual switch(es) associated with the VM. 
Multiple NICs can now be assigned.
* **State**: State of the VM: { Running | Paused | Off }
* **Path**: Folder where the VM data will be stored
* **Generation**: Virtual machine generation { 1 | 2 }.
Generation 2 virtual machines __only__ support VHDX files.
* **SecureBoot**: Enables or disables secure boot __only on generation 2 virtual machines__.
If not specified, it defaults to True.
* **StartupMemory**: Startup RAM for the VM 
* **MinimumMemory**: Minimum RAM for the VM. 
Setting this property enables dynamic memory.
* **MaximumMemory**: Maximum RAM for the VM.
Setting this property enables dynamic memory.
* **MACAddress**: MAC address(es) of the VM.
Multiple MAC addresses can now be assigned.
* **ProcessorCount**: Processor count for the VM
* **WaitForIP**: If specified, waits for the VM to get valid IP address
* **RestartIfNeeded**: If specified, will shutdown and restart the VM as needed for property changes
* **Ensure**: Ensures that the VM is Present or Absent

The following xVMHyper-V properties **cannot** be changed after VM creation:

* VhdPath
* Path
* Generation

### xVMSwitch

* **Name**: The desired VM Switch name 
* **Type**: The desired type of switch: { External | Internal | Private }
* **NetAdapterName**: Network adapter name for external switch type
* **AllowManagementOS**: Specify if the VM host has access to the physical NIC
* **BandwidthReservationMode**: Specify the QoS mode used (options other than NA are only supported on Hyper-V 2012+): { Default | Weight | Absolute | None | NA }.
* **Ensure**: Ensures that the VM Switch is Present or Absent 

### xVhdFile

* **VhdPath**: Path to the VHD 
* **FileDirectory**: The FileDirectory objects to copy to the VHD (as used in the "File" resource). 
Please see the Examples section for more details. 

### xVMNetworkAdapter

* **Id**: Unique string for identifying the resource instance. This is the key property for the instances of this resource.
* **Name**: Name of the network adapter as it appears either in the management OS or attached to a VM.
* **SwitchName**: Virtual Switch name to connect the adapter to.
* **VMName**: Name of the VM to attach to. If you want to attach new VM Network adapter to the management OS, set this property to 'Management OS'.
* **DynamicMacAddress**: Set this to $false if you want to specify a static MAC address.
* **StaticMacAddress**: Specifies static MAC address for the Network adapter.
* **Ensure**: Ensures that the VM Network Adapter is Present or Absent.
* 
Please see the Examples section for more details. 

## Versions

### Unreleased

### 3.7.0.0
* Adding a new resource
	* MSFT_xVMNetworkAdapter: Attaches a new VM network adapter to the management OS or VM.

### 3.6.0.0

* xVHD: Updated incorrect property name MaximumSize in error message

### 3.5.0.0
* Converted appveyor.yml to install Pester from PSGallery instead of from Chocolatey.

* MSFT_xVMHyperV: Fixed bug in Test-TargetResource throwing when a Vhd's ParentPath property was null.

### 3.4.0.0

* MSFT_xVMHyperV: Fixed bug causing Test-TargetResource to fail when VM had snapshots.
* MSFT_xVMHyperV: Adds localization support.
* MSFT_xVMSwitch: Fixes bug where virtual switches are duplicated when BandwidthReservationMode is not specified.

### 3.3.0.0

* xHyperV: Added SecureBoot parameter to enable control of the secure boot BIOS setting on generation 2 VMs.
-  Fixed drive letter when mounting VHD when calling resource xVhdFile. Fixes #20.
* MSFT_xVMHyperV: Changed the SwitchName parameter to string[] to support assigning multiple NICs to virtual machines.
* MSFT_xVMHyperV: Changed the MACAddress parameter to string[] to support assigning multiple MAC addresses to virtual machines.
* MSFT_xVMHyperV: Added enabling of Guest Service Interface.
* MSFT_xVMSwitch: Added the BandwidthReservationMode parameter which specifies how minimum bandwidth is to be configured on a virtual switch

### 3.2.0.0

* Added data type System.String to CheckSum parameter of Get/Set/Test-TargetResource functions and aligned indentation.
* Minor fixes

### 3.1.0.0
* xVMHyperV: Fixed bug in mof schema (Generation property had two types)
* xVhdFileDirectory: Fixed typo in type comparison
* Readme updates

### 3.0.0.0

* Decoupled VM generation from underlying VHD format in xVMHyperV resource.
 * __Breaking change:__ xVMHyperV resource: Generation property type changed from a String to an Integer.
 * The initial generation property was tied to the virtual disk format which was incorrect and has been rectified.
 * This change will only impact configurations that have previously explicitly specified the VM generation is either "vhd" or "vhdx".

### 2.4.0.0

* Fixed VM power state issue in xVMHyperV resource

### 2.3.0

* Fixed check for presence of param AllowManagementOS.

### 2.2.1

### 2.1

* Added logic to automatically adjust VM's startup memory when only minimum and maximum memory is specified in configuration
* Fixed the issue that a manually stopped VM cannot be brought back to running state with DSC

### 2.0

* Added xVhdFileDirectory Resource
* Allowed name to be specified with the extension in xVhd (e.g. the Vhd name could either be "sample" or "sample.vhd")
* When a VHD cannot be removed because it is already being used by another process, an error will be thrown.

### 1.0.0.0

* Initial release with the following resources
    - xVhd 
    - xVMHyperV 
    - xVMSwitch  

## Examples

### End-to-End Example

In this example, we ensure that an internal VM Switch and a VM made from VHD with an unattended.xml file are present.

```powershell
# End to end sample for x-Hyper-v
Configuration Sample_EndToEndXHyperV_RunningVM
{

    param
    (
        [Parameter(Mandatory)]
        $baseVhdPath,

        [Parameter(Mandatory)]
        $name,
        [Parameter(Mandatory)]
        [validatescript({Test-Path $_})]
        $unattendedFilePathToCopy

    )

    Import-DscResource -module xHyper-V

    # Create a switch to be used by the VM
    xVMSwitch switch
    {
        Name =  "Test-Switch"
        Ensure = "Present"      
        Type = "Internal"
    }

    # Create new VHD file.
    xVHD NewVHD1
    {

            Ensure         = "Present"
            Name           = $name
            Path           = (Split-Path $baseVhdPath)
            Generation     = "vhd"
            ParentPath     =  $baseVhdPath

    }

    # Customize VHD by copying a folders/files to the VHD before a VM can be created
    # Example below shows copying unattended.xml before a VM can be created
    xVhdFile CopyUnattendxml
    {
            VhdPath =  $vhdPath
            FileDirectory =  MSFT_xFileDirectory {
                               SourcePath = $unattendedFilePathToCopy
                               DestinationPath = "unattended.xml"
                               }

    }

    # create the generation 1 testVM out of the vhd.
    xVMHyperV testvm
    {
        Name = "$($name)_vm"
        SwitchName = "Test-Switch"
        VhdPath = Join-path (Split-Path $baseVhdPath) "$name.vhd"
        ProcessorCount = 2
        MaximumMemory  = 1GB
        MinimumMemory = 512MB
        RestartIfNeeded =  "TRUE"
        DependsOn = "[xVHD]NewVHD1","[xVMSwitch]switch","[xVhdFile]CopyUnattendxml"
        State = "Running"

    }

}
```

### Create a new VHD

This configuration will create a new VHD on Hyper-V host.

```powershell
Configuration Sample_xVHD_NewVHD
{
    param
    (
        [Parameter(Mandatory)]
        [string]$Name,

        [Parameter(Mandatory)]
        [string]$Path,

        [Parameter(Mandatory)]
        [Uint64]$MaximumSizeBytes,

        [ValidateSet("Vhd","Vhdx")]
        [string]$Generation = "Vhd",

        [ValidateSet("Present","Absent")]
        [string]$Ensure = "Present"        
    )

    Import-DscResource -module xHyper-V

    Node localhost
    {
        xVHD NewVHD
        {
            Ensure           = $Ensure
            Name             = $Name
            Path             = $Path
            Generation       = $Generation
            MaximumSizeBytes = $MaximumSizeBytes
        }
    }
}
```

### Create a differencing VHD

This configuration will create a differencing VHD, given a parent VHD, on a Hyper-V host.

```powershell
Configuration Sample_xVhd_DiffVHD
{
    param
    (
        [Parameter(Mandatory)]
        [string]$Name,

        [Parameter(Mandatory)]
        [string]$Path,

        [Parameter(Mandatory)]
        [string]$ParentPath,

        [ValidateSet("Vhd","Vhdx")]
        [string]$Generation = "Vhd",

        [ValidateSet("Present","Absent")]
        [string]$Ensure = "Present"    
    )

    Import-DscResource -module xHyper-V

    Node localhost
    {
        xVHD DiffVHD
        {
            Ensure     = $Ensure
            Name       = $Name
            Path       = $Path
            ParentPath = $ParentPath
            Generation = $Generation
        }
    }
}
```

### Create a secure boot generation 2 VM for a given VHD

This configuration will create a VM, given a VHDX, on Hyper-V host.

```powershell
Configuration Sample_xVMHyperV_Simple
{
    param
    (
        [string[]]$NodeName = 'localhost',

        [Parameter(Mandatory)]
        [string]$VMName,

        [Parameter(Mandatory)]
        [string]$VhdxPath    
    )

    Import-DscResource -module xHyper-V

    Node $NodeName
    {
        # Install HyperV feature, if not installed - Server SKU only
        WindowsFeature HyperV
        {
            Ensure = 'Present'
            Name   = 'Hyper-V'
        }

        # Ensures a VM with default settings
        xVMHyperV NewVM
        {
            Ensure     = 'Present'
            Name       = $VMName
            VhdPath    = $VhdxPath
            Generation = 2
            DependsOn  = '[WindowsFeature]HyperV'
        }
    }
} 
```

### Create a VM with dynamic memory for a given VHD

This configuration will create a VM with dynamic memory settings, given a VHD, on Hyper-V host.
If not specified, Secure Boot will be enabled by default for generation 2 VMs.

```powershell
Configuration Sample_xVMHyperV_DynamicMemory
{
    param
    (
        [string[]]$NodeName = 'localhost',

        [Parameter(Mandatory)]
        [string]$VMName,

        [Parameter(Mandatory)]
        [string]$VhdPath,

        [Parameter(Mandatory)]
        [ValidateSet(1,2)]
        [unit32]$Generation,

        [Parameter(Mandatory)]
        [Uint64]$StartupMemory,

        [Parameter(Mandatory)]
        [Uint64]$MinimumMemory,

        [Parameter(Mandatory)]
        [Uint64]$MaximumMemory,
        
        [Parameter()]
        [Boolean]$SecureBoot = $true  
    )

    Import-DscResource -module xHyper-V

    Node $NodeName
    {
        # Install HyperV feature, if not installed - Server SKU only
        WindowsFeature HyperV
        {
            Ensure = 'Present'
            Name   = 'Hyper-V'
        }

        # Ensures a VM with dynamic memory
        xVMHyperV NewVM
        {
            Ensure        = 'Present'
            Name          = $VMName
            VhdPath       = $VhdPath
            Generation    = $Generation
            SecureBoot    = $SecureBoot
            StartupMemory = $StartupMemory
            MinimumMemory = $MinimumMemory
            MaximumMemory = $MaximumMemory
            DependsOn     = '[WindowsFeature]HyperV'
        }
    }
}
```

### Create a VM with dynamic memory, network interface and processor count for a given VHD

This configuration will create a VM with dynamic memory, network interface and processor count settings, given a VHD, on Hyper-V host.

```powershell
Configuration Sample_xVMHyperV_Complete
{
    param
    (
        [string[]]$NodeName = 'localhost',

        [Parameter(Mandatory)]
        [string]$VMName,

        [Parameter(Mandatory)]
        [string]$VhdPath,

        [Parameter(Mandatory)]
        [ValidateSet(1,2)]
        [unit32]$Generation,

        [Parameter(Mandatory)]
        [Uint64]$StartupMemory,

        [Parameter(Mandatory)]
        [Uint64]$MinimumMemory,

        [Parameter(Mandatory)]
        [Uint64]$MaximumMemory,

        [Parameter(Mandatory)]
        [String]$SwitchName,

        [Parameter(Mandatory)]
        [String]$Path,

        [Parameter(Mandatory)]
        [Uint32]$ProcessorCount,

        [ValidateSet('Off','Paused','Running')]
        [String]$State = 'Off',
        
        [Parameter()]
        [Boolean]$SecureBoot = $true,

        [Switch]$WaitForIP
    )

    Import-DscResource -module xHyper-V

    Node $NodeName
    {
        # Install HyperV feature, if not installed - Server SKU only
        WindowsFeature HyperV
        {
            Ensure = 'Present'
            Name   = 'Hyper-V'
        }

        # Ensures a VM with all the properties
        xVMHyperV NewVM
        {
            Ensure          = 'Present'
            Name            = $VMName
            VhdPath         = $VhdPath
            SwitchName      = $SwitchName
            State           = $State
            Path            = $Path
            Generation      = $Generation
            SecureBoot      = $SecureBoot
            StartupMemory   = $StartupMemory
            MinimumMemory   = $MinimumMemory
            MaximumMemory   = $MaximumMemory
            ProcessorCount  = $ProcessorCount
            MACAddress      = $MACAddress
            RestartIfNeeded = $true
            WaitForIP       = $WaitForIP 
            DependsOn       = '[WindowsFeature]HyperV'
        }
    }
}
```

### Create VM with multiple NICs.

This configuration will create two internal virtual switches and create a VM with two network interfaces, one attached to each virtual switch.

```powershell
Configuration Sample_xVMHyperV_MultipleNICs
{
    param
    (
        [string[]]$NodeName = 'localhost',

        [Parameter(Mandatory)]
        [string]$VMName,

        [Parameter(Mandatory)]
        [string]$VhdPath,
        
        [Parameter(Mandatory)]
        [string[]]$SwitchName,
        
        [Parameter()]
        [string[]]$MACAddress
    )

    Import-DscResource -module xHyper-V

    Node $NodeName
    {
        # Install HyperV feature, if not installed - Server SKU only
        WindowsFeature HyperV
        {
            Ensure = 'Present'
            Name   = 'Hyper-V'
        }
        
        # Dynamically build the 'DependsOn' array for the 'xVMHyperV' feature
        # based on the number of virtual switches specified
        $xVMHyperVDependsOn = @('[WindowsFeature]HyperV')
        
        # Create each virtual switch
        foreach ($vmSwitch in $SwitchName)
        {
            # Remove spaces and hyphens from the identifier
            $vmSwitchName = $vmSwitch -replace ' ','' -replace '-',''
            # Add the virtual switch dependency
            $xVMHyperVDependsOn += "[xVMHyperV]$vmSwitchName"
            
            xVMSwitch $vmSwitchName
            {
                Ensure         = 'Present'
                Name           = $vmSwitch
                Type           = 'Internal'
                DependsOn      = '[WindowsFeature]HyperV'
            }
        }

        # Ensures a VM with all the properties
        xVMHyperV $VMName
        {
            Ensure     = 'Present'
            Name       = $VMName
            VhdPath    = $VhdPath
            SwitchName = $SwitchName
            MACAddress = $MACAddress
            # Use the dynamically created dependency list/array
            DependsOn  = $xVMHyperVDependsOn
        }
    }
}

Sample_xVMHyperV_MultipleNICs -VMName 'MultiNICVM' -VhdPath 'C:\VMs\MultiNICVM.vhdx' -SwitchName 'Switch 1','Switch-2'
```

### Create an internal VM Switch

This configuration will create an internal VM Switch, on Hyper-V host.

```powershell
Configuration Sample_xVMSwitch_Internal
{
    param
    (
        [string[]]$NodeName = 'localhost',

        [Parameter(Mandatory)]
        [string]$SwitchName
    )

    Import-DscResource -module xHyper-V

    Node $NodeName
    {
        # Install HyperV feature, if not installed - Server SKU only
        WindowsFeature HyperV
        {
            Ensure = 'Present'
            Name   = 'Hyper-V'
        }

        # Ensures a VM with default settings
        xVMSwitch InternalSwitch
        {
            Ensure         = 'Present'
            Name           = $SwitchName
            Type           = 'Internal'
            DependsOn      = '[WindowsFeature]HyperV'
        }
    }
}
```

### Create an external VM Switch

This configuration will create an external VM Switch, on Hyper-V host.

```powershell
Configuration Sample_xVMSwitch_External
{
    param
    (
        [string[]]$NodeName = 'localhost',

        [Parameter(Mandatory)]
        [string]$SwitchName,

        [Parameter(Mandatory)]
        [string]$NetAdapterName        
    )

    Import-DscResource -module xHyper-V

    Node $NodeName
    {
        # Install HyperV feature, if not installed - Server SKU only
        WindowsFeature HyperV
        {
            Ensure = 'Present'
            Name   = 'Hyper-V'
        }

        # Ensures a VM with default settings
        xVMSwitch ExternalSwitch
        {
            Ensure         = 'Present'
            Name           = $SwitchName
            Type           = 'External'
            NetAdapterName = $NetAdapterName 
            DependsOn      = '[WindowsFeature]HyperV'
        }
    }
}
```

### Copy a file or folder into a VHD  

```powershell
Configuration xVhdD_CopyFileOrFolder
{

     Param(
     [Parameter(Mandatory=$true, Position=0)]
     [validatescript({Test-Path $_})]
     $vhdPath,
     [Parameter(Mandatory=$true)]
     [validatescript({Test-Path $_})]
     $itemToCopy,
     [Parameter(Mandatory=$true)]
     $relativeDestinationPath
    )

        Import-DscResource -moduleName xHyper-V

        xVhdFile FileCopy
        {
            VhdPath =  $vhdPath
            FileDirectory =  MSFT_xFileDirectory {
                               SourcePath = $itemToCopy
                               DestinationPath = $relativeDestinationPath
                               }

        }

}
```
### Change an Attribute for a File  

```powershell 
Configuration ChangeAttribute
{
    param(
      [Parameter(Mandatory=$true, Position=0)]
      [validatescript({Test-Path $_})]
      $vhdPath,
      [Parameter(Mandatory=$true)]
      $relativeDestinationPath,
      [ValidateSet ("Archive", "Hidden",  "ReadOnly", "System" )] $attribute
    )

    Import-DscResource -moduleName xHyper-V
      xVhdFile Change-Attribute
        {
            VhdPath =  $vhdPath
            FileDirectory =  MSFT_xFileDirectory {
                               DestinationPath = $relativeDestinationPath
                               Attributes  = $attribute
                               }

        }
}
```
