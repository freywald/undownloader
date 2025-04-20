# undownloader

A frontend for yt-dlp – for you and me.

<br/>

Exemplary, a typical – non-atypical – use case:

> I had no clue I have a playlist on YouTube.
>
&mdash; <cite><i>the unknown one</i></cite>
<br/><br/>
> How do I make a living off rapping when no one knows me? 
> I got kind of discouraged so I had created a playlist of pop songs.
>
&mdash; <cite><i>the other one</i></cite>

## Add links to a specific processing queue via console aliases/shortcuts

* da \<link\>: adds link to the *audio* processing queue.
* dv \<link\>: adds link to the *video* processing queue.
* dav \<link\>: adds link to the *audio+video* processing queue.
* dap \<link\>: adds link to the *audio_playlist* processing queue.
* das \<link\>: adds link to the *audio_simple* processing queue.

### Example #1: using default queues

#### 1. Add multiple links.
```bash
$ da https://www.youtube.com/watch?v=Ou8c2Mf7nU0
The data 'https://www.youtube.com/watch?v=Ou8c2Mf7nU0' was appended to the processing queue 'audio'.
```

#### 2. Start download.
When done with collecting links, run ``undownloader`` to start processing of the default queues [audio, video, audio+video, audio_playlist]

```bash
undownloader
```

#### 3. Done.
<hr>
<br/>

### Example #2: archive and watch content changes

You need a wrapper script that calls undownloader and a link list.

#### 1. Use a custom wrapper script with your configuration.

**update-archive.sh**
```bash
#!/bin/bash
clear

link_list_locator="audio:link-list.txt"
target_directory="Music"

if (( ${#@} == 0 )); then
	echo "HELP"
	echo
	echo
	echo "To download the content from the link list and create a download queue, execute the following command."
	echo "$ $0 --run"
	echo
	echo "If new links were added to the link list, execute the following command, in order to also update the download queue."
	echo "$ $0 --update"
	echo
	echo "To forget which links have been downloaded already and overwrite all existing files with a fresh online copy, execute the following command."
	echo "The download queue is not updated."
	echo "$ $0 --force-download --force-overwrites"
	echo
	echo "To completely rebuild the archive, execute the following command."
	echo "The download queue is regenerated and reflects the current state of the link list, in other words: new links are recognized."
	echo "$ $0 --update --force-download --force-overwrites"
	exit 1
fi

undownloader --from-file="$link_list_locator" --target-directory="$target_directory" "$@"

```

Also provide a link list which contains links to be downloaded and can be updated regularly.

**link-list.txt**
```text
https://www.youtube.com/watch?v=jNQXAC9IVRw
https://www.youtube.com/watch?v=Ou8c2Mf7nU0
```

#### 2. Call the script any time you want.

```bash
./update-archive.sh
```

#### 3. Done.
<hr>
<br/>

## Example #3: Configuration templates

Each queue references a template file which defines the queue instance.

**extract of 'audio.template'**
```bash
__download_with_configuration 
# run the program yt-dlp with these options:…
yt-dlp
`# quality and file format` \
--extract-audio \
--remux mka \
`# thumbnails` \
--write-thumbnail \
--convert-thumbnails png \
--embed-thumbnail \
`# subtitles` \
--sub-lang en \
--write-auto-subs \
--write-sub \
--convert-subs srt \
--embed-subs \
`# embed files` \
--embed-metadata \
--embed-info-json \
--embed-chapters \
```

<br/>
<hr>

## Installation

1. Please execute the following shell command in your shell.
```bash
path="$HOME/bin/applications/undownloader" && \
mkdir --parents -- "$path" && \
git clone -- git@github.com:freywald/undownloader.git "$path" && \
cd -- "$path" && \
git remote set-url --push origin push_disabled && \
./install && \
echo "Installation successful."
```
2. Installation successful.
3. Done.
4. You're done.
