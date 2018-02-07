# Mendeley PMID Updater for Windows

## Full Source Code Discloser

This is an adaptation of the code from [Mendeley PMID Updater for Mac / Linux](https://github.com/powerpak/mendeley-pmid-updater) by [Theodore Pak](https://github.com/powerpak), which has been updated to allow the script to run in Windows.
[Mendeley](http://www.mendeley.com/) is a pretty nice (and free!) reference manager that syncs to an online account (currently with 2GB of  storage for free accounts).  While it imports PDFs as citable references with remarkable accuracy, and is wonderful for a cite-while-you-write workflow, it doesn't always pick up PMIDs and PMCIDs.  Certain citation styles, such as [those used by the NIH for grant proposals](http://publicaccess.nih.gov/include-pmcid-citations.htm), require you to add these IDs to your bibliography.

It scans your Mendeley database for journal articles and does the following:

1. If the PMID is available, it is used to retrieve the PMCID and DOI.
2. If there is no PMID, but the DOI is available, it is used to retrieve the PMCID and PMID.

Whatever can be retrieved that is not present in your database is updated accordingly.


The [PMC ID Converter](http://www.ncbi.nlm.nih.gov/pmc/tools/id-converter-api/) and [EUtils](http://www.ncbi.nlm.nih.gov/books/NBK25501/) APIs are used to try to match your IDs, in that respective order.

## Data Backup

**Read this carefully.** This will back-up your Mendeley database every time it runs, as a separate file into a sub-folder, `backups/`.  However, it does operate outside of Mendeley's public APIs for accessing your data. 

If losing data concerns you, and it should, manually backup your Mendeley library before using this.  You can do this by running Mendeley and looking in the `Help` menu for `Create Backup...`

The script operates locally on your Mendeley database, which ends in `.sqlite` and is named after your Mendeley username, and can be found in:

* %userprofile%\AppData\Local\Mendeley Ltd\Mendeley Desktop

When you make a backup as previously described, Mendeley hands you a ZIP archive of this folder, which will also include all the files for your imported articles.  This script simply copies the aforementioned `.sqlite` file into a `backups` folder before it operates on it.

## Requirements

You'll need Ruby ≥1.9.2, RubyGems, and some basic gems, which will be installed with Bundler.

## Usage

First, clone this repository to a directory and `cd` into it.  Then:

    $ bundle install

to install the required gems.  Bundler may prompt you for your password if it needs to write to a system directory.  Of the gems needed, Nokogiri is probably the most finicky to install, so you may need to refer to [its installation instructions](http://nokogiri.org/tutorials/installing_nokogiri.html) if it cannot compile a certain library—but usually it can do it without intervention.  Then:

    $ rake

will perform a dry-run of the matching procedure.  If everything goes well, you will see the script start running through journal articles in your Mendeley database, reporting if it can cross-match them using the aforementioned APIs.  If it looks like it is doing a good job, you can stop the process with Ctrl-C, then:

    $ rake update_ids

will start saving these IDs to your Mendeley database.  **Technical Note:** because Mendeley has no dedicated PMCID field, the PMCID is mapped to its [CSL variable](http://citationstyles.org/downloads/specification.html#standard-variables) by jamming it into the Notes field [as specified here](http://support.mendeley.com/customer/portal/articles/723677-adding-new-variables-to-my-citation-style).  From there, it can be [parsed out by citeproc-js](http://gsl-nagoya-u.net/http/pub/citeproc-doc.html#supplementary-fields) to be included in your bibliography.

These tasks will not run unless Mendeley is shut down, and every time it runs, you will first see a new `.sqlite` file backed up to the `backups/` folder in this repo, just in case something goes horribly wrong.  In the rare event that this occurs, remove the numerical prefix and copy it back to the appropriate folder listed in the [Fair Warning](#fair-warning) section of this README.

The next time you start Mendeley, you will see your new DOIs, PMIDs, and PMCIDs, and it will automatically sync them to your online account, so your other computers will receive these IDs too.  **Note:** I've had some trouble syncing the PMCIDs, since it doesn't seem that the Notes field is reliably synced.  This seems to be a [common Mendeley issue](http://support.mendeley.com/customer/portal/questions/476985-synchronizing-notes-between-desktops).  You may need to run this script again on other computers if you need the PMCIDs there.

If you want to use a citation that prints these IDs after your references in your bibliography, check out [this CSL](http://csl.mendeley.com/styles/100600971/national-library-of-medicine-grant-proposals-9).  You can install it for Mendeley Desktop by going to `View` > `Citation Style` > `Journal Abbreviations` ... changing to the `Get More Styles` tab, and pasting the URL at the bottom.

## License

The MIT License (MIT)

Copyright (c) 2014 Theodore Pak

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
