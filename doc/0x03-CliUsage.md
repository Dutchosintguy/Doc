# 0x02 - CLI Usage

## Initializing

```bash
$ Goca -h
  -domain string
    	Scrape domain
  -dorkpages int
    	Number of pages to dork form the search engine (default 1)
  -filetype string
    	Look for metadata on (default "*")
  -folder string
    	Run goca against local folder
  -listplugins
    	List available plugins
  -listurls
    	Just list url do not process them
  -loglevel string
    	Log level
  -term string
    	Dork term
  -timeout int
    	Timeout per request (default 30)
  -ua string
    	User-Agent to be used.
  -url string
    	Scope Goca acctions to a domain
```

***

## Plugins

```bash
$ Goca -listplugins
Plugins for: ppt
  - ppt

Plugins for: mp3
  - mp3

Plugins for: image
  - image

Plugins for: dsstore
  - dsstore

Plugins for: odp
  - odp

Plugins for: ods
  - ods

Plugins for: xls
  - xls

Plugins for: doc
  - doc

Plugins for: odt
  - odt

Plugins for: pdf
  - pdf
```

***

## Debug level

By default it is disabled. You can set `loglevel` flag to `debug` and `info`:

`info` log level:

```bash
$ Goca -url "google.com" -loglevel info
INFO[0000] Fear The Goca!
INFO[0000] Version: (dev) () built on
WARN[0000] Running Goca with all plugins
{
	"main_type": "PDF",
	"target": "https://research.google.com/pubs/archive/44268.pdf",
	"create_date": "2015-06-05T10:32:36-04:00",
	"modify_date": "2015-08-27T15:53:14+02:00",
	"metadata_date": "2015-08-27T15:53:14+02:00",
	"creator_tool": "Causal Productions Pty Ltd",
	"document_id": "uuid:812cbd70-5b7b-472c-be5c-f3c3d0cd2c19",
	"instance_id": "uuid:bcc79f57-f422-41af-a873-36fb75d00f84",
	"content_type": "application/pdf",
	"title": "Large Vocabulary Automatic Speech Recognition for Children",
	"producer": "pdfTeX-1.40.14"
}
``` 

`debug` log level:

```bash
$ Goca -url "google.com" -loglevel debug
INFO[0000] Fear The Goca!
INFO[0000] Version: (dev) () built on
WARN[0000] Running Goca with all plugins
DEBU[0000] Dorks for plugin *: [{google site:google.com inurl:.DS_Store intitle:index.of} {google site:google.com intext:.DS_Store & intitle:index -github} {google site:google.com intitle:index.of +?last modified? +?parent directory? +(mp3|wma|ogg) -htm -html -php -asp} {google site:google.com filetype:mp3} {google site:google.com filetype:mp4} {google site:google.com filetype:m4a} {google site:google.com filetype:m4p} {google site:google.com filetype:m4v} {google site:google.com filetype:3gp} {google site:google.com filetype:3g2} {google site:google.com filetype:flac} {google site:google.com filetype:mp3} {google site:google.com filetype:mp4} {google site:google.com filetype:m4a} {google site:google.com filetype:m4p} {google site:google.com filetype:m4v} {google site:google.com filetype:3gp} {google site:google.com filetype:3g2} {google site:google.com filetype:mp3} {google site:google.com filetype:mp4} {google site:google.com filetype:m4a} {google site:google.com filetype:m4p} {google site:google.com filetype:m4v} {google site:google.com filetype:3gp} {google site:google.com filetype:3g2} {google site:google.com filetype:ogg} {google site:google.com filetype:ogv} {google site:google.com filetype:oga} {google site:google.com filetype:ogx}]
DEBU[0000] Executing plugin mp3
DEBU[0000] Executing plugin odp
DEBU[0000] Executing plugin pdf
DEBU[0000] Executing plugin ppt
DEBU[0000] Executing plugin dsstore
DEBU[0000] Executing plugin image
DEBU[0000] Executing plugin ods
DEBU[0000] Executing plugin odt
DEBU[0000] Executing plugin xls
DEBU[0000] Executing plugin doc
```

***

## Looking for metadata in one site

To look metadata in just one site:

```bash
Goca -url "google.com" -loglevel info
{
	"main_type": "PDF",
	"target": "https://research.google.com/pubs/archive/44268.pdf",
	"create_date": "2015-06-05T10:32:36-04:00",
	"modify_date": "2015-08-27T15:53:14+02:00",
	"metadata_date": "2015-08-27T15:53:14+02:00",
	"creator_tool": "Causal Productions Pty Ltd",
	"document_id": "uuid:812cbd70-5b7b-472c-be5c-f3c3d0cd2c19",
	"instance_id": "uuid:bcc79f57-f422-41af-a873-36fb75d00f84",
	"content_type": "application/pdf",
	"title": "Large Vocabulary Automatic Speech Recognition for Children",
	"producer": "pdfTeX-1.40.14"
}
```

## Look for metadata from a specified term

```bash
Goca -term "Chema Alonso"
{

}
```

## Other options

It is possible also to specify which file type do you want to search:

```
$ Goca -url "target.com" -term "Money" -filetype docx,xlsx,mp3
``` 

