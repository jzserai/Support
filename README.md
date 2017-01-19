<b>Releases</b>
 
- 1.0.0 - Initial release  
- 1.0.1 - Bug fix  
- 1.0.2 - Bug fix, readme formatting  
- 1.0.3 - Readme formatting  
- 1.0.4 - Readme update  
- 1.0.5 - Added version history  
- 1.0.6 - Added more information about project version (current)  
 
A build task that applies a full, point-in-time version number to .Net assemblies and DacPacs.
 
#### Overview ####
It is almost always true that by the time a build is ready for Staging, User Acceptance, or some other pre-production environment, the developer or team leader can establish the major, minor, and patch (if used) numbers.  These version numbers should be checked in with the latest changes before the build to ensure the final assembly  and DacPac versions are correct.  
To summarize, before the project is built in VSTS or TFS, the AssemblyInfo.cs|.vb file should have all 3 assembly  version attributes setwith Major.Minor.0.0. The AssemblyInofrmationalVersion (aka Product Version) may have a  third patch number as well.   
Ex: Major = 5, Minor = 1, Patch = 1: <b>5.1.1</b>

Version numbers can be created with 2, 3, and 4 version components.  
Major.Minor are always used at a minimum and are expected to be correct in the AssemblyInfo file in the project. 
An AssemblyInfo.cs file is automatically added to SQL Server Database projects by selecting the SQLCLR property page 
in the project properties and clicking the Assembly Information ... button.

![](/Visual Studio 2015 SSDT Project Properties - SQLCLR Tab.png?raw=true "SSDT Project Properties")

 
With the Assembly Information dialog open, enter the minimum information, which is Major, Minor, and Assembly Title. 
Assembly Title is used to ensure the correct assembly gets its version updated when there are multiple projects in a 
solution and more than one AssemblyInfo file exists. If you want to version the assemblies in a solution with many 
projects, simply add this build task for each project and ensure the AssemblyInfo file is properly filled in. This 
allows you to have unique versions for each project if desired.  

![](/Visual Studio 2015 SSDT Project Properties - Assembly Information Dialog.png?raw=true "Assembly Information Dialog")
 
If you want to use Semantic versioning for the Product Version, which is actually the AssemblyInformationalVersion 
attribute, then add  the third version component called the patch number. See [Semantic Versioning 2.0.0](http://semver.org/) 
for the definition and how to use it. 
However, you cannot edit the AssemblyInformationalVersion in the Assembly Information 
dialog. Instead, open the AssemblyInfo.cs file in your text editor and manually add the attribute to the end right after 
AssemblyFileVersion, as shown. 

![](/Visual Studio 2015 SSDT Project Properties - AssemblyInfo.cs With User Updates.png?raw=true "Assembly Information")
 
 
Following the .Net Framework model, it is best to use 4 part versions for both the AssemblyVersion and 
AssemblyFileVersion attributes. Use semantic versioning for the AssemblyInformationalVersion attribute. Then, 
use the AssemblyInformationalVersion for display in your software. The other two version attributes are better 
suited for internal use and tracking.  
 
After the build completes, the assembly will have all three version attributes set correctly. To verify, download the build 
output from VSTS and unzip it to your hard drive. Navigate to the folder with MyDatabase.dll and display its properties. 
Your assembly will be named with the value you entered in the project properties SQLCLR property page under Assembly name 
at the top. The default value for this  property is the project name. There is seldom reason to change it.

![](/Visual Studio 2015 SSDT Build Output Assembly Properties.png?raw=true "SSDT Project Build Output File Properties")
 
If you elected to also version a DacPac, you can verify it is correctly set by navigating to the build folder you extracted 
to your  local hard drive where the MyDatabase.dacpac file resides. Right-click it and select <b>Unpack...</b> .

![](/File Explorer DacPac Unpack.png?raw=true "Unpack the DacPac file")
 
A command window will appear along with an Unpack Microsoft SQL Server DAC Package File dialog to select the destination  
of the unpacked files. Accept the default and click the Unpack button. After a few moments a folder will appear in the 
current folder named the same as the DacPac file. Enter the folder and open the DacMetadata.xml file. Verify the <DacVersion> 
xml element has the correct version number in it.  
  
DotNetVersioner takes 7 mandatory arguments:
 
<div style="background-color:White; color:DarkBlue;>
 
##### Project folder #####
 
 
 
    This is the directory you want versioner to start its search for your project's AssemblyInfo.[vb|cs] file. 
    The search is recursive so you can freely specify the project directory.
 
   

##### Number of components in AssemblyVersion #####
 
 
    This is the desired number of components in the AssemblyVersion. If 4 is specified, for example, 
    then the AssemblyVersion will be set to the Major.Minor values you originally set in the 
    AssemblyInfo file. The third and fourth parts are the BuildNumber and revision. The BuildNumber 
    is the 2 digit year and the 3 digit day of year. In VSTS this can be specified with the build 
    variables   
      
    $(Year:yy)$(DayOfYear)  
     
    The revision is set by VSTS when the build starts. It is extracted from the $(Build.BuildNumber) 
    build variable and requires that the $(Rev:r) build variable is the final component of the build 
    number defined on the General tab of the build definition. An example build number format is  
      
    $(Build.DefinitionName)\_$(Year:yyyy)\_$(Month)\_$(DayofMonth)\_$(Year:yy)\_$(DayOfYear)\_$(Rev:r)  
 
    The underscores are used here so that this build number can be used when naming the 
    Server Artifact where the build output is stored. Using periods makes this very unwieldy.
 
 

##### Number of components in AssemblyFileVersion #####
 
 
 
    This is the desired number of components in the AssemblyFileVersion. If 4 is specified, 
    for example, then the AssemblyFileVersion will be set to the Major.Minor values you 
    originally set in the AssemblyInfo file. The third and fourth parts are the BuildNumber 
    and revision. The BuildNumber is the 2 digit year and the 3 digit day of year. In VSTS 
    this can be specified with the build variables  
      
    $(Year:yy)$(DayOfYear)
 
    The revision is set by VSTS when the build starts. It is extracted from the $(Build.BuildNumber) 
    build variable and requires that the $(Rev:r) build variable is the final component of the build 
    number defined on the General tab of the build definition. An example build number format is  
    
    $(Build.DefinitionName)\_$(Year:yyyy)\_$(Month)\_$(DayofMonth)\_$(Year:yy)\_$(DayOfYear)\_$(Rev:r)  
   
    The underscores are used here so that this build number can be used when naming the 
    Server Artifact where the build output is stored. Using periods makes this very unwieldy.
 
 

##### Number of components in AssemblyInformationalVersion #####
 
 
 
    This is the desired number of components in the AssemblyInformationalVersion. If 4 is 
    specified, for example, then the AssemblyInformationalVersion will be set to the Major.Minor 
    values you originally set in the AssemblyInfo file. The third and fourth parts are the 
    BuildNumber and revision. The BuildNumber is the 2 digit year and the 3 digit day of year. 
    In VSTS this can be specified with the build variables   
      
    $(Year:yy)$(DayOfYear)  
    
    The revision is set by VSTS when the build starts. It is extracted from the $(Build.BuildNumber) 
    build variable and requires that the $(Rev:r) build variable is the final component of the build 
    number defined on the General tab of the build definition. An example build number format is  
    
    $(Build.DefinitionName)\_$(Year:yyyy)\_$(Month)\_$(DayofMonth)\_$(Year:yy)\_$(DayOfYear)\_$(Rev:r)   
      
    The underscores are used here so that this build number can be used when naming the Server Artifact 
    where the build output is stored. Using periods makes this very unwieldy.
 
 
 
    
##### Assembly Title #####
 
 
 
    The AssemblyTitle title attribute value in the AssemblyInfo.* file from which you want to 
    read the major and minor versions.
 
 

##### Set DacPac version? #####
 
 
 
    If the project is a Sql Server Database project (*.sqlproj), set this to Yes to apply the 
    AssemblyFileVersion to the DacVersion property of the project before it is built.
 
    This should be a 4-part version number.
 
    Note: If the DacPac is subsequently applied to a live database instance by SQLPackage.exe, 
    this version number will be inserted or updated in the msdb.dbo.sysdac_instances_internal table.
 
    View the registered Data-tier Applications by running the view msdb.dbo.sysdac_instances.
 
 

##### Project file #####
 
 
 
    The SQL Server Database project file itself. Ex: MyDatabaseProject.sqlproj  
    
    This is used only if 'Set DacPac version' is yes.
    

</div>
