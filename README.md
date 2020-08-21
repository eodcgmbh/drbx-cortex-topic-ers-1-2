# The DRBX Cortex Topic Definition for ERS-1/2

## Introduction.

The topic definition is a Java package that allows identifying ERS-1/2 products by applying regular expressions-like file name matching rules. The central element of this package is the ontology definition file ([cortex-index.owl](src/main/resources/META-INF/cortex-index.owl)) located in the META-INF folder inside the resources directory.

The topic is currently designed to support the following product types from the ERS-1/2 satellite mission:

- ASPS products (AMI sensor)
  - ASPS20.H
  - ASPS20.N
- ATSR sensor
  - AT1\_AR\_\_2P
  - AT1\_NR\_\_2P
  - AT1\_TOA\_1P
  - AT2\_AR\_\_2P
  - AT2\_TOA\_1P
  - AT2\_NR\_\_2P
- RA sensor
  - ERS\_ALT\_2\_
  - ERS\_ALT\_2M
  - ERS\_ALT\_2S

For decoding the binary datasets, which is necessary for extracting metadata and the footprint coordinates, product-specific XML schema definitions are contained in the ([xsd](src/main/resources/META-INF/xsd)) folder inside the resources directory. Where available, the metadata information is extracted from the netCDF file.

The file name matching patterns and the XSD files were created based on the official product specification documents:

- [ASPS Product Handbook](https://earth.esa.int/documents/700255/1743979/EA-TN-WP1-120302_issue1.2.pdf/5576ea75-8b8a-4c98-becf-eab2bdeec08a)
- [ASPS Product Format](https://earth.esa.int/documents/700255/1743979/ERSE-GSEV-EOPG-RS-06-0002_ver2.5_ASPS_Product_Format.pdf/b58b6e60-e0b4-4adc-9dca-685c00f0c5e3)
- [Envisat-style products for ATSR-1 and ATSR-2 data ](https://earth.esa.int/documents/10174/437508/ATSR-L1B-and-L2-Products-Envisat-Format-Rel-3-0.pdf)
- [ENVISAT-1 PRODUCTS SPECIFICATIONS, VOLUME 7: AATSR PRODUCTS SPECIFICATIONS](https://earth.esa.int/documents/700255/2042507/Vol-07-Aats-4C.pdf/71cd7964-7860-4df7-abe5-5042b76cc31f)
- [REAPER Product Handbook for ERS Altimetry Reprocessed Products](https://earth.esa.int/documents/10174/1511090/Reaper-Product-Handbook-3.1.pdf)

Apache Maven is used for building the Java project. The Project Object Model XML file ([pom.xml](pom.xml)) specifies the overall package configuration.

## Product Format and Composition

### ASPS Products

The considered products from the AMI sensor (ASPS) are provided in the netCDF format, i.e. one file per dataset:

- one netCDF file having the extension .nc

Examples for these products include:

- ASPS20\_H\_931113133451.nc
- ASPS20\_N\_980922033121.nc

No compression is applied in this case and the netCDF files are ingested directly into DHuS.

Metadata and footprints are created based on the information contained within the netCDF file.

### ATSR Sensor

The ATSR products are comprised of three or four physical files, which are delivered separately:

- a mixed ASCII/BINARY data file having the extension .E1 for ERS-1 and .E2 for ERS-2
- a log file containing processing logs with the extension .log
- an md5 checksum file having the extension .md5
- for TOA products additionally a text file having the extension .OT

Metadata is extracted from the E1/E2 ASCII header, footprints are created based on the records contained within the E1/E2 binary section.

In order for the DHuS ingest to work, these two individual files need to be combined into one ZIP archive first. The filename of the archive needs to be exactly the same as for the other files, with the obvious difference of having the file extension ".zip".

The following is an example illustrating the structure for the ATSR products, after the previous step of ZIP archive creation:

- AT1\_AR\_\_2PURAL19951118\_174642\_000000008008\_00183\_22716\_0000.zip
  - AT1\_AR\_\_2PURAL19951118\_174642\_000000008008\_00183\_22716\_0000.E1
  - AT1\_AR\_\_2PURAL19951118\_174642\_000000008008\_00183\_22716\_0000.E1.log
  - AT1\_AR\_\_2PURAL19951118\_174642\_000000008008\_00183\_22716\_0000.E1.md5

Note that the file that will be ingested in DHuS is the AT1\_AR\_\_2PURAL19951118\_174642\_000000008008\_00183\_22716\_0000.zip.

### RA Sensor

The considered products from the RA sensor (REAPER) are provided in the netCDF format, i.e. one file per dataset:

- one netCDF file having the extension .NC

Examples for these products include:

- E1\_REAP\_ERS\_ALT\_2M\_19950810T165017\_19950810T182941\_RP01.NC
- E2\_REAP\_ERS\_ALT\_2\_\_19950515T161633\_19950515T175536\_RP01.NC
- E2\_REAP\_ERS\_ALT\_2S\_19950515T112228\_19950515T130053\_RP01.NC

No compression is applied in this case and the netCDF files are ingested directly into DHuS.

Metadata and footprints are created based on the information contained within the netCDF file.


## Defined Classes for ERS-1/2

### Class &ers12;productASPS

The &ers12;productASPS class identifies the ASPS products from the AMI sensor based on file name matching rules. The expected format is netCDF. This class must match the corresponding ASPS class name referenced by the metadata extractor in the ERS-1/2 addon (separate Java package). 

### Class &ers12;productATSR

The &ers12;productATSR class identifies the ERS-1/2 ATSR products. This class must match the corresponding class name referenced by the metadata extractor in the ERS-1/2 addon (separate Java package). It is the base class for all ATSR products.

### Class &ers12;productATSR_zip

The &ers12;productATSR_zip is a subclass of &ers12;productATSR and identifies the zipped ERS-1/2 ATSR products.

### Class &ers12;productATS_AR__2P

The &ers12;productATS_AR__2P is a subclass of &ers12;productATSR and identifies the ATS_AR__2P product E1 or E2 file. It also references the corresponding XML schema file.

### Class &ers12;productATS_NR__2P

The &ers12;productATS_NR__2P is a subclass of &ers12;productATSR and identifies the ATS_NR__2P product E1 or E2 file. It also references the corresponding XML schema file.

### Class &ers12;productATS_TOA_1P

The &ers12;productATS_TOA_1P is a subclass of &ers12;productATSR and identifies the ATS_TOA_1P product E1 or E2 file. It also references the corresponding XML schema file.

### Class &ers12;productRA

The &ers12;productRA class identifies the REAPER products from the RA sensor based on file name matching rules. The expected format is netCDF. This class must match the corresponding RA class name referenced by the metadata extractor in the ERS-1/2 addon (separate Java package). 


