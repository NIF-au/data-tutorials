# Data Handling, Anonymisation, Provenance and Curation

This tutorial should take approximately an hour, the data for the exercises is available here:

   https://github.com/NIF-au/data-tutorials/blob/master/dm01-examples.tar.gz

You can use gitprint to download/print a PDF of this file:

   https://gitprint.com/NIF-au/data-tutorials/blob/master/dm01-anonymisation-provenance.md

## 1 Introduction to Neurodebian
All the exercises in this tutorial will be performed in an instance of the NeuroDebian VM, in most cases this will have been done for 
you already and can be run either locally via VirtualBox or on a cloud provider such as Amazon. If you don't have a running version of NeuroDebian, you can download a copy from neuro.debian.net/vm, install VirtualBox and then import the downloaded virtualbox OVA file.

Note that you could also easily complete this tutorial on a desktop install of NeuroDebian or even OSX but you may need to complete the package installation via other means.

As per the Neurodebian download site, the default username and password that are used are:

    username: brain
    password: neurodebian

The example data for this tutorial should then be downloaded within the Virtual Machine usine the inbuilt browser or via wget on the command line:
 
    $ cd ~/Desktop
    $ wget https://github.com/NIF-au/data-tutorials/blob/master/dm01-examples.tar.gz

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

    $ sudo apt-get install minc-tools itksnap

apt-get will install any needed package dependencies as part of the install process. You can also get an idea of which 
packages (and versions) are currently installed using the dpkg command:

    $ dpkg --list

This will allow you to rapidly build a list of installed software and versions that you have used in an analysis.

## 3 Installing data packages
In addition to the datasets available as part of common analysis tools (FSL, freesurfer) NeuroDebian also pacakges a number of existing atlases and other datasets as part of the packaging process. You can see the current list of datasets available here:

    http://neuro.debian.net/pkglists/toc_pkgs_for_release_data.html

Choose a data package name that you are interested in and install it either by the `apt-get` method or by using the Synaptic Package manager in:

    Applications -> System -> Synaptic Package Manager

## 4 example DICOM file - structure of DICOM

The DICOM medical imaging format has been used for many years in medical imaging. It is supported by most if not all medical imaging systems but is not the most widely used analysis format in research, especially in the field of brain imaging.

A DICOM file is nearly always a single slice of a medical imaging dataset and a number with matching metadata (same series) are used to reconstruct a single 3D image. Each of the DICOM files will contain most of the the metadata for the entire study.

There is a typical DICOM structure from a scanning session in the DICOM-examples folder on the desktop, it consists of multiple 3D and multi-slice acquisitions. First launch a Terminal window (black icon at the bottom) and then unzip the compressed BRAINIX.zip archive:

    $ cd ~/Desktop/DICOM-examples/
    $ unzip BRAINIX.zip

While viewers such as OSIRIX (OSX), Imagemagick, ImageJ and SanteDicomViewer (Windows) can view this data, we'll use the more powerfull DICOM tools in the dcmtk package to have a look at the headers.

If you haven't installed the dcmtk package in neurodebian, you can do this as such:

    $ sudo apt-get install dcmtk

We can now dump all the headers of the first file as such:

````
$ dcmdump BRAINIX/BRAINIX/IRM\ cérébrale\,\ neuro-crâne/T1-3D-FFE-C\ -\ 801/IM-0001-0001.dcm

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

    $ dcmdump +r --scan-directories BRAINIX > dicom-headers.txt

The resulting file will be just over 40,000lines of text! remember that this header information is repeated for each file.

## 5 How to anonymise (DICOM, MINC, Nifti)

In order to anonymise data we can use the dcmodify tool (from dcmtk). In the first example we will remove the Patient birth date:

    $ dcmodify -ea '(0010,0030)' <infile.dcm>
    
    $ dcmodify -ie -nb -imt -gin \
       -ea "(0010,0010)" -ea "(0010,0030)" -ea "(0008,0050)" -ea "(0020,000D)" \
       -ea "(0020,000E)" -ea "(0008,0018)" -ea "(0008,0080)" -ea "(0008,0081)" \
       -ea "(0008,1070)" -ea "(0008,1155)" -ea "(0010,1000)" -ea "(0020,0010)" \
       -ea "(0020,4000)" <infile.dcm>

The above wll remove the most common tags that include identifying information (DatasetTitle, Patient birth date, Accession number, Study Instance UID, Series Instance UID, SOP Instance UID, Institution Name, Institution Address, Operator Name, Referenced SOP Instance UID, Other Patient Ids, Study ID and Image Comments). Note that this will have to run on each of the individual files that make up a DICOM series.

Nifti data typically does not need anonymising beyond any details in the file name itself as the fixed sized header doesn't leave much space for such data. You can determine what 


## 6 Dataset conversion between Nifti, MINC and DICOM

#6.5 - File naming, no spaces, no blah blah
      - windows vs linux vs OSX (case sensitive)

#7 - Differences in metadata
      - NIFTI
      - MINC
      - ISMRMRD - wget https://sourceforge.net/projects/ismrmrd/files/data/simple_gre.h5
      - https://ismrmrd.github.io/

#8 - provenance in MINC file vs Nifti (no metadata so have to use script)

#9 - Create releasable fileset
   - always include code versions with data
      - how to get the versions of packages you are using (dpkg -- list)
   - always include parameters with data
   - be sure to clean history from data (anonymise)
      - perhaps best is via Nifti

#9.1 - learn to script (bash)

#10 - versioning

#11 - Brief introduction to Licencing

#12 - upload to figshare/mendelay?
      - self host? (bad)
      - let's use github

#13 - create a simple github repo (online)
      - intialise with files
      - add README.md + COPYING.md
      - Add versioning via tags
      - Note how versioning turns into release

#14 - create a DOI




Cloud-based analysis, compression and storage





sudo apt-get install minc-tools dicomnifti fsl-complete dcmtk itksnap
