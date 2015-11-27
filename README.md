# Configuration for Drupal 8 documentation with diagrams

Drupal has fully embraced Object-Oriented Programming since version 8. But I don't know the internals well so thought having class inheritance diagrams would come handy. However, [the official Drupal API documentation](https://www.drupal.org/project/api] does not come with such diagrams so I decided to generate them myself. The result can be seen at http://drupal8docs.diasporan.net. It isn't complex, but there are various switches that I had to press to generate the documentation in the way I wanted e.g. in HTML, with code excerpts, custom search and Google Analytics tracking code. I'm sharing the config with the hope that it may help others who would like to generate the doc themselves.

## Prerequisites
### Software applications
You need to install Doxygen and Graphviz to generate the documentation and diagrams. Detailed instructions are found [here|https://www.stack.nl/~dimitri/doxygen/manual/install.html].

I simply ran:
```
sudo apt-get install doxygen graphvis
```
to install them on my Ubuntu server. Note the Doxygen package for Ubuntu 14.04 does not come with files required for external search feature. If you want it, you need to install Doxygen manually.

### Hardware
It seems you need minimum 2GB of RAM. I first tried generating the documentation on a VM hosted on Digital Ocean, which came with 1GB of RAM, but it failed because of insufficient memory. After upsizing the instance with 2GB of RAM, it successfully generated the documentation with diagrams.

## Config
### doxygen config file
doxygen.conf is the config file that tells Doxygen how to generate the documentation. I created the initial config using Doxywizard, then referred to the documentation to enable/disable various options.

There are few things you need to edit, such as the output path. I've added placeholders like ```[EDIT: blahblah]``` so replace them accordingly.

Configuration options are found [here](https://www.stack.nl/~dimitri/doxygen/manual/config.html)

If you want to start afresh, run the following command to generate a config file:
```
doxygen -g [filename]
```
...which will generate a config file with the filename you specified. If given no file names, the command will generate a file called 'Doxyfile'. 

### Header and footer
As described below, I inserted some things into the header and footer. To do so, it's best to start from the default header and footer, which can be generated using the following command:
```
doxygen -w html header.html footer.html stylesheet.css [config file name]
```

### Search functionality
I tried to get [Doxygen's search features|https://www.stack.nl/~dimitri/doxygen/manual/searching.html] working, but I encountered some issues:
* Client side searching didn't work. I guess the volume of the documentation was too large for it
* Server side searching with external indexing didn't work out-of-box on Ubuntu 14.04 because ['doxysearch.cgi' and doxyindexer didn't come in the [package|http://packages.ubuntu.com/trusty/amd64/doxygen/filelist]
* Even after I reinstalled Doxygen manually, the search results were only returned in json (found no instruction on how to convert it into a consumable format without extensive works to theme it)

...so in the end I looked to Google and simply embedded a custom search form through the header template (custom_header.html). You can set one up for free at https://cse.google.co.uk

### Google Analytics
Since it would be interesting to see the stats, I embedded a GA tracking code through the custom footer (custom_footer.html)

## Generating the documentation
Generating the documentation is very easy althoug it takes quite a while because of the large number of files it needs to output. Once you've fulfilled all the prerequisites, run:
```bash
doxygen [config file name] 
```
If you don't have the right permission to the output directory specified in the doxygen config file, it will fail. Adjust the directory permissions or run with ```sudo```.


## Hosting the documentation
As I mentioned above, it takes a VM with a 2GB of RAM to generate the documentation. Running such an instance would cost me $20/mo. This seems a bit wasteful considering the site is static. I therefore copied over all the files to S3 and it would cost me less than ¢20 per month including the fee for Route 53, which is *ridiculously* cheap! 
* [s3cmd](http://s3tools.org/s3cmd) is a simple CLI tool that allow you to put / get / sync files on Amazon S3
* [Documentation on how to host static websites on S3 with custom domains](https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html).
