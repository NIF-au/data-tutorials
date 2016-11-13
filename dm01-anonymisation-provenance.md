# Data Handling, Anonymisation, Provenance and Curation

This tutorial should take approximately an hour, the data for the exercises is available here:

   https://github.com/NIF-au/data-tutorials/blob/master/dm01-examples.tar.gz

You can use gitprint to download/print a PDF of this file:

   https://gitprint.com/NIF-au/data-tutorials/blob/master/dm01-anonymisation-provenance.md

## 1 Introduction to Neurodebian
All the exercises in this tutorial will be performed in an instance of the NeuroDebian VM, in most cases this will have been done for 
you already and can be run either locally via VirtualBox or on a cloud provider such as Amazon. If you don't have a running version of NeuroDebian, you can download a copy from neuro.debian.net/vm, install VirtualBox and then import the downloaded virtualbox OVA file.

Note that you could also easily complete this tutorial on a desktop install of NeuroDebian or even OSX but you may need to complete the package installation via other means.

To get started with NeuroDebian, follow the instructions here:

   http://neuro.debian.net/vm.html

As per the Neurodebian download site, the default username and password that are used are:

    username: brain
    password: neurodebian

The example data for this tutorial should then be downloaded within the Virtual Machine usine the inbuilt browser or via wget on the command line:
 
    $ cd ~/Desktop
    $ wget https://github.com/NIF-au/data-tutorials/raw/master/dm01-examples.tar.gz

Then unzip the example files in your NeuroDebian instance:

    $ cd ~/Desktop
    $ tar zxvf dm01-examples.tar.gz

## 2 Installing software
Neurodebian is based upon the Debian operating system, if you've used Ubuntu before it too is based upon Debian so the 
apt-get based install system may be familiar. There are a number of pre-installed packages in the menus, when these are
selected for the first time they will be installed. In order to install MRview you only need to select the menu item and
the software will be installed in the Neurodebian VM:

    Applications -> Neurodebian -> Medical Imaging -> MRview

The MRview package will then be installed.  You can also install pacakges from the command line by running apt-get on the
command line (launch a terminal using the black icon from the panel launcher at the bottom). To install the itksnap package 
you could do this:

    $ sudo apt-get install itksnap
    
Check that itksnap is working by either selecting itksnap from the application menu (Applications -> NeurDebian -> Medical Imaging -> ITK-SNAP) or by launching it from the command line:

    $ itksnap

It is a good habit to get into using the command line to install packages as it will allow you to keep a record of which 
packages you need for an analysis, for example to install both minc-tools and itksnap you can do this:

    $ sudo apt-get install minc-tools itksnap fsl-complete dicomnifti

apt-get will install any needed package dependencies as part of the install process. This may take a small amount of time so you can leave this running and open a second Terminal window to continue with the tutorial. 

You can get an exact list of which packages (and versions) are currently installed using the dpkg command, for example use this sequence of commands to return a list of packages with the keywork atlas (grep is a unix search tool):

```
$ dpkg --list | grep atlas
iU  fsl-atlases                                   5.0.7-2                              all          FSL's MNI152 standard space stereotaxic brain atlases
iU  fsl-bangor-cerebellar-atlas                   5.0.7-2                              all          probabilistic atlas of the human cerebellum
iU  fsl-harvard-oxford-atlases                    5.0.7-2                              all          probabilistic atlas of human cortical brain areas (lateralized)
iU  fsl-jhu-dti-whitematter-atlas                 5.0.7-2                              all          human brain white-matter atlas
iU  fsl-juelich-histological-atlas                5.0.7-2                              all          brain atlas based on cyto- and myelo-architectonic segmentations
iU  fsl-mni-structural-atlas                      5.0.7-2                              all          hand-segmented single-subject human brain atlas
iU  fsl-oxford-striatal-atlas                     5.0.7-2                              all          probabilistic atlas of the human brain's sub-striatal regions
iU  fsl-oxford-thalamic-connectivity-atlas        5.0.7-2                              all          probabilistic brain atlas of thalamic white-matter connectivity
iU  fsl-resting-connectivity-parcellation-atlases 5.0.7-2                              all          brain parcellations based on resting state connectivity
iU  fsl-subthalamic-nucleus-atlas                 5.0.7-2                              all          probabilistic subthalamic nucleus atlas
iU  fsl-talairach-daemon-atlas                    5.0.7-2                              all          structural anatomy labels of the Talairach atlas
```

This will allow you to rapidly build a list of installed software and versions that you have used in an analysis.

## 3 Installing data packages
In addition to the datasets available as part of common analysis tools (FSL, freesurfer) NeuroDebian also pacakges a number of existing atlases and other datasets as part of the packaging process. You can see the current list of datasets available here:

    http://neuro.debian.net/pkglists/toc_pkgs_for_release_data.html

Choose a data package name that you are interested in and install it either by the `apt-get` method or by using the Synaptic Package manager in:

    Applications -> System -> Synaptic Package Manager

## 4 example DICOM file - structure of DICOM

The DICOM medical imaging format has been used for many years in medical imaging. It is supported by most if not all medical imaging systems but is not the most widely used analysis format in research, especially in the field of brain imaging.

A DICOM file is nearly always a single slice of a medical imaging dataset and a number with matching metadata (same series) are used to reconstruct a single 3D image. Each of the DICOM files will contain most of the the metadata for the entire study.

There is a typical DICOM structure from a scanning session in the DICOM-examples folder on the desktop, it consists of multiple 3D and multi-slice acquisitions. First launch a Terminal window (black icon at the bottom) and then shift to the examples directory:

    $ cd ~/Desktop/dm01-examples

While viewers such as OSIRIX (OSX), Imagemagick, ImageJ and SanteDicomViewer (Windows) can view this data, we'll use the more powerfull DICOM tools in the dcmtk package to have a look at the headers. DICOM files typically use the extension .dcm or .IMA

If you haven't installed the dcmtk package in neurodebian, you can do this as such:

    $ sudo apt-get install dcmtk

We can now dump all the headers of the first file as such:

```
$ dcmdump BRAINX-neuro/T1-3D-FFE-C-801/IM-0001-0001.dcm

# Dicom-File-Format
...
# Dicom-Data-Set
# Used TransferSyntax: Little Endian Explicit
(0008,0005) CS [ISO_IR 100]                             #  10, 1 SpecificCharacterSet
(0008,0008) CS [ORIGINAL\PRIMARY\M_FFE\M\FFE]           #  28, 5 ImageType
(0008,0012) DA [20061201]                               #   8, 1 InstanceCreationDate
(0008,0013) TM [145346.000000]                          #  14, 1 InstanceCreationTime
(0008,0014) UI [1.3.46.670589.11.8743.5]                #  24, 1 InstanceCreatorUID
(0008,0016) UI =MRImageStorage                          #  26, 1 SOPClassUID
(0008,0018) UI [1.3.46.670589.11.0.0.11.4.2.0.8743.5.5396.2006120114483570701] #  62, 1 SOPInstanceUID
(0008,0020) DA [20061201]                               #   8, 1 StudyDate
(0008,0021) DA [20061201]                               #   8, 1 SeriesDate
(0008,0022) DA [20061201]                               #   8, 1 AcquisitionDate
(0008,0023) DA [20061201]                               #   8, 1 ContentDate
(0008,0030) TM [141645.000000]                          #  14, 1 StudyTime
(0008,0031) TM [144406.780000]                          #  14, 1 SeriesTime
(0008,0032) TM [144406.780000]                          #  14, 1 AcquisitionTime
(0008,0033) TM [144406.780000]                          #  14, 1 ContentTime
(0008,0050) SH [0]                                     #   2, 1 AccessionNumber
(0008,0060) CS [MR]                                     #   2, 1 Modality
(0008,0070) LO [Philips Medical Systems]               #  24, 1 Manufacturer
(0008,0080) LO [7GEFF0GbzqCNo43Yd0,Ibu,zQSSX]           #  28, 1 InstitutionName
(0008,0090) PN [dAEvNTxZJO0E]                           #  12, 1 ReferringPhysicianName
(0008,1010) SH [intera]                                 #   6, 1 StationName
...
(0010,0010) PN [BRAINIX]                               #   8, 1 PatientName
(0010,0020) LO [5Yp0E]                                 #   6, 1 PatientID
(0010,0030) DA [19490301]                               #   8, 1 PatientBirthDate
(0010,0040) CS [0000]                                   #   4, 1 PatientSex
(0010,1030) DS [0]                                     #   2, 1 PatientWeight
(0010,21c0) US 4                                        #   2, 1 PregnancyStatus
```

Note that it contains a large amount of identifying information, DICOM is organised as a series of elements (eg: 0010,0020 refers to the PatientID), these by convention and international agreement relate to fields via what is called a DICOM dictionary, dcmdump wraps up thie process for you and outputs the elements, the value and the description.

We can also then dump all the headers of the files in a directory to a text file as such:

    $ dcmdump +r --scan-directories BRAINIX-neuro > dicom-headers.txt

The resulting file will be just over 40,000lines of text! remember that this header information is repeated for each file.

## 5 Dataset conversion between Nifti, MINC and DICOM

There are converters between most imaging file formats but support for all metadata differs. The most problematic is the conversion from DICOM to other formats as there is no easy way to deal with the multiple headers from multiple 2D input files that must be merged to form a 3D Nifti or MINC image.  First we can convert a DICOM series to MINC

```
$ dcm2mnc 1010_brain_mr_14_jpg/* .
Checking file types...
File 1010_brain_mr_14_jpg/IM-0001-0001.dcm appears to be DICOM (CD/Export).
Parsing 32 files     |<--                                                  -------------------------------------------------->
Sorting 32 files...   Done sorting files.
Processing files, one series at a time...
-Parsing series info |<--                                                  -------------------------------------------------->
-Creating minc file  |<--                                                  -------------------------------------------------->
Done processing files.
```
The output MINC file will be put in the current directory (the '.' in the input command line). We can determine basic information from the file via the mincinfo command:

```
$ mincinfo brain_mr_20080801_000000/brain_mr_20080801_000000_14e1_mri.mnc 
file: brain_mr_20080801_000000/brain_mr_20080801_000000_14e1_mri.mnc
image: unsigned short 0 to 4095
image dimensions: zspace yspace xspace
    dimension name         length         step        start
    --------------         ------         ----        -----
    zspace                     32            5     -49.3523
    yspace                    256    -0.898438      144.349
    xspace                    216    -0.898438      94.6099
```

We can then convert from MINC to Nifti:

    $ mnc2nii brain_mr_20080801_000000/brain_mr_20080801_000000_14e1_mri.mnc  result.nii
   
There is also the dicomnifit package that allows you to convert direct from DICOM to Nifto:

    $ dinifit <input-dir> <output.nii>
   
## 6 How to anonymise (DICOM, MINC, Nifti)

In order to anonymise **DICOM** data we can use the dcmodify tool (from dcmtk). In the first example we will remove the Patient birth date:

    $ dcmodify -ea '(0010,0030)' <infile.IMA>
    
    $ dcmodify -ie -nb -imt -gin \
       -ea "(0010,0010)" -ea "(0010,0030)" -ea "(0008,0050)" -ea "(0020,000D)" \
       -ea "(0020,000E)" -ea "(0008,0018)" -ea "(0008,0080)" -ea "(0008,0081)" \
       -ea "(0008,1070)" -ea "(0008,1155)" -ea "(0010,1000)" -ea "(0020,0010)" \
       -ea "(0020,4000)" <infile.dcm>

The above wll remove the most common tags that include identifying information (DatasetTitle, Patient birth date, Accession number, Study Instance UID, Series Instance UID, SOP Instance UID, Institution Name, Institution Address, Operator Name, Referenced SOP Instance UID, Other Patient Ids, Study ID and Image Comments). Note that this will have to run on each of the individual files that make up a DICOM series.

**Nifti** data typically does not need anonymising beyond any details in the file name itself as the fixed sized header doesn't leave much space for such data. You can determine what is in the header via the fslhd command:

    $ . /etc/fs/fsl.sh
    $ fslhd result.nii

The only free form text field is the "descrip" field that typically contains the last command run on a file

The best way to anonymise a **MINC** file is to not include the identifying information when converting

    $ dcm2nii -anon <input_dir> <output_dir>
   
There are MINC tools specifically written to anonymise a MINC file such as mincanon and mincclean, you can see what is in the header of a MINC file as such:

```
$ mincheader brain_mr_20080801_000000/brain_mr_20080801_000000_14e1_mri.mnc
netcdf brain_mr_20080801_000000_14e1_mri {
dimensions:
	zspace = 32 ;
	yspace = 256 ;
	xspace = 216 ;
variables:
	double zspace(zspace) ;
		zspace:varid = "MINC standard variable" ;
		zspace:vartype = "dimension____" ;
		zspace:version = "MINC Version    1.0" ;
		zspace:comments = "Z increases from patient inferior to superior" ;
		zspace:spacing = "regular__" ;
		zspace:alignment = "centre" ;
		zspace:step = 5. ;
		zspace:start = -49.3522932361199 ;
		zspace:spacetype = "native____" ;
		zspace:direction_cosines = 4.60341405991035e-17, -0.0314107603760001, 0.999506560324944 ;
	int yspace ;
		yspace:varid = "MINC standard variable" ;
		yspace:vartype = "dimension____" ;
		yspace:version = "MINC Version    1.0" ;
		yspace:comments = "Y increases from patient posterior to anterior" ;
		yspace:spacing = "regular__" ;
		yspace:alignment = "centre" ;
		yspace:step = -0.8984375 ;
		yspace:start = 144.348985857155 ;
		yspace:spacetype = "native____" ;
		yspace:direction_cosines = 2.05103400000001e-10, 0.999506560324944, 0.0314107603760001 ;
	int xspace ;
		xspace:varid = "MINC standard variable" ;
		xspace:vartype = "dimension____" ;
		xspace:version = "MINC Version    1.0" ;
		xspace:comments = "X increases from patient left to right" ;
		xspace:spacing = "regular__" ;
		xspace:alignment = "centre" ;
		xspace:step = -0.8984375 ;
		xspace:start = 94.6099424301985 ;
		xspace:spacetype = "native____" ;
		xspace:direction_cosines = 1., -2.050022e-10, -6.4425e-12 ;
	short image(zspace, yspace, xspace) ;
		image:parent = "rootvariable" ;
		image:varid = "MINC standard variable" ;
		image:vartype = "group________" ;
		image:version = "MINC Version    1.0" ;
		image:signtype = "unsigned" ;
		image:valid_range = 0., 4095. ;
		image:complete = "true_" ;
		image:image-min = "--->image-min" ;
		image:image-max = "--->image-max" ;
	int rootvariable ;
		rootvariable:varid = "MINC standard variable" ;
		rootvariable:vartype = "group________" ;
		rootvariable:version = "MINC Version    1.0" ;
		rootvariable:parent = "" ;
		rootvariable:children = "image\n",
			"patient\n",
			"study\n",
			"acquisition\n",
			"dicominfo\n",
			"dicom_groups" ;
  ...
```

Note that typically the combined DICOM headers will be included in a MINC file. You will see these further down the file.

Remember that we also mentioned that differing instrument manufacturers implement the DICOM standard differently? Siemens for example has a technology called Pheonix that allows to you drop any DICOM file from a siemens instrument into the scan card on another and all the parameters will be imported. So, this means that all this information must also be in the headers. We'll now use a UNIX text editor called vi (https://en.wikipedia.org/wiki/Vi) to find it. We are going to open a DICOM file in a text editor, you could try other ones but not all interpret the binary information successfully. vi is an editor with a steep learning curve but I would encourage you to at least try to learn some basic functionality as some point as it is very easy to run over a remote connection.

First we open the file:

    $ vi 7T-Siemens-angio.IMA

Siemens happens to encode this information in plain text in the DICOM file starting with ASCCONV, we can now search for this using vi. type the following:

    /
    
This enters search mode, they type:

    ASCCONV
    
and press enter, you should get to a section of the file that looks like this:

```
      <ParamBool.""save_orig"">  { ""true""  }
    }
  }  
}    
### ASCCONV BEGIN ###                                                                                                            
ulVersion                                = 0x14b44b6
tSequenceFileName                        = ""%SiemensSeq%\fl_tof""
tProtocolName                            = ""TOF+AF8-3D+AF8-multi-slab""
tReferenceImage0                         = ""1.3.12.2.1107.5.2.34.18975.2014021415554018922430014""
tReferenceImage1                         = ""1.3.12.2.1107.5.2.34.18975.2014021416114473598633511""
tReferenceImage2                         = ""1.3.12.2.1107.5.2.34.18975.2014021415554487212630127""
ucScanRegionPosValid                     = 0x1
ucTablePositioningMode                   = 0x2
sProtConsistencyInfo.tBaselineString     = ""N4_VB17A_LATEST_20090307""
sProtConsistencyInfo.tSystemType         = ""095""
sProtConsistencyInfo.flNominalB0         = 6.98
sProtConsistencyInfo.flGMax              = 40
sProtConsistencyInfo.flRiseTime          = 5
sProtConsistencyInfo.lMaximumNofRxReceiverChannels = 32
sGRADSPEC.sEddyCompensationX.aflAmplitude[0] = 0.00164875
sGRADSPEC.sEddyCompensationX.aflAmplitude[1] = -5.36951e-005
sGRADSPEC.sEddyCompensationX.aflAmplitude[2] = 0.0055199
sGRADSPEC.sEddyCompensationX.aflAmplitude[3] = 0.00122196
sGRADSPEC.sEddyCompensationX.aflAmplitude[4] = 0.000758568
...
```

You can now scroll up and down using the UP and DOWN arrow keys, use Ctrl-F and Ctrl-B to move one page up/down at a time. Ntoe that this section has the capacity to also encode sensitive information and it is all hidden away in a non-standard variable that you wouldn't typically remove when anoymising DICOM. 

To exit vi press ESC (Escape) to exit search mode and then type:

    :q!

yes, vi is strange, but a good strange, trust me on the learn the basic functionality bit, you'll thank me when you have to debug something running on a server that is 7000km away one day.

Given that DICOM is at times an unknown quantity, arguably the best way to distribute data is in either NIFTI or MINC formats in which we know everything that is in the headers.
