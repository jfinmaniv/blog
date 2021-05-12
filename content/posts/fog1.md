---
title: "Under the Weather, Part 1"
description: "Creating a High-resolution Fog Map Using MODIS Satellite Data"
date: 2020-12-17T08:04:12-08:00
draft: false
---

This series presents an hedonic analysis of fog in the city of Seaside, CA,
near the Monterey Peninsula. Hedonic analysis is a method of estimating the
value of something that does not have a price. The inspiration for this series
was a school assignment I originally collaborated on with Winn McEnery. Part 1
focuses on creating a high-resolution (250 m) fog map using MODIS satellite
from NASA.

### MODIS

The Moderate Resolution Imaging Spectroradiometer
([MODIS](https://modis.gsfc.nasa.gov/about/)) is a remote sensing instrument
that collects data about the Earth's surface and atmosphere.  Compared to
other remote sensing platforms, such as Landsat, MODIS data has higher
temporal resolution but lower spatial resolution. There are two currently
deployed MODIS instruments, each of which survey the entire Earth's surface on
a daily basis. MODIS collects radiation data in 36 spectral bands which vary
in resolution from 250 m to 1 km. The (bi-)daily snapshot of atmospheric
conditions enable accurate measurement of local differences in weather
patterns, such as fog frequency, at the scale of a residential neighborhood.

There are many ([MODIS products](https://modis.gsfc.nasa.gov/data/)) to choose
from. After some shopping I determined that the [Cloud
Mask](https://modis.gsfc.nasa.gov/data/dataprod/mod35.php), intended primarily
for quality assurance of other products, would be the most appropriate for
this project despite being an "off-label" use case. Cloud Mask data from each
satellite are packaged separately as MOD35_L2 and MYD35_L2; because I
considered only one snapshot per day to be sufficient I search for and
downloaded only MOD35_L2 data.

After some dead-ends, I found NASA's [Earthdata
Search](https://search.earthdata.nasa.gov/search) to be the most reliable
place to find, subset, and download large amounts of MODIS data. The site does
require an account but this does not involve anything more than verifying an
email address (I have yet to receive any spam). Searching for MOD35_L2 yielded
over two million "granules", or individual snapshots. Placing a pin on the
interactive map over the city of Seaside, CA, reduced this to about 17,000
granules. The 250-m resolution bands comprise the visible spectrum and are
only captured by day-time shots. Applying a day-only granule filter brought
the number of granules down to a manageable 8,474.

Simply clicking "download" will not initiate a bulk download of the data but rather
generate a list of links to the desired files, in this case 8,474 of them.
Thankfully the Earthdata team also provides a download script that can be used
to automate the process. It took about three days of running this script in
the background to download all the files. I found it necessary to modify the
script to avoid starting over each time the connection was lost by adding an
if statement around the download command:

{{< highlight bash "linenos=table" >}}
if ! [ -f $stripped_query_params ]
then
    curl -f -b "$cookiejar" -c "$cookiejar" -L --netrc-file "$netrc" -g -o $stripped_query_params -- $line && echo || exit_with_error "Command failed with error. Please retrieve the data manually."
fi
{{< / highlight >}}
