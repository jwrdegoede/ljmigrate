# ljmigrate 1.5 with dreamwidth support

A tool for archiving posts from any LiveJournal clone site and optionally 
migrating them to any other LJ-clone site. Useful for moving from LiveJournal
to GreatestJournal or InsaneJournal, for instance.

Based on ljdump; original ljdump license & header at the bottom of the single
source file. http://hewgill.com/software/ljdump/

Extensive modifications by  C J Silverio.
Version 1.5
18 April 2009

BSD licence mumbo-jumbo to follow. By which I mean, do what you want 
with the source. If you find it useful, excellent!

To get usage information:

	./ljmigrate --help 

## Running new versions of the tools

If you ran the tool once, migrated most of your posts, but failed to migrate some because of bugs, you might want to run a newer version but NOT re-migrate everything. Here's how to do that.

The folder named after your account has all the interesting data in it. You can copy it and move it around. You can move it from the folder holding the *old* version of the tool into the new one. Run the new version. Tada!

## To use: expert version

Make sure your journal has been converted to Unicode, if it's an older one.

Edit the sample config options in ljmigrate.cfg.

Edit to point the tool to your LJ account and the account you're migrating to.

If you're not migrating, set the "migrate" config value to 0 or False.

Run ./ljmigrate.py.

Wait. DON'T INTERRUPT IT. I'm not yet very good about saving state as I go, so you'll get double-posts if you interrupt it in the middle and run it again.

If the migration failed for many of your posts, because of LiveJournal flakiness or troubles with your network connection, you can re-run the tool to retry the  migration:

	./ljmigrate.py --retry

This will skip entries it successfully moved before.

## To use: long version

* If your journal is an older one, you might need to convert it to Unicode.
Visit this url and follow the instructions. If your journal was created later than 2004, or if you've ever done this conversion before, you don't need to do this.

		http://www.livejournal.com/settings/?c=OldEncoding
		http://www.livejournal.com/support/faqbrowse.bml?faqid=143&q=unicode&lang=

* Edit the config file: `ljmigrate.cfg`
  Make sure you use that EXACT name! Many editors will try to force on their filename ending, which will make the config file unfindable by the tool. For best results use TextEdit to edit the file. (Other options are vim, emacs, BBEdit, TextWrangler, and TextMate.)

* Edit the settings in the config file.

		[settings]
		migrate: True
		generate-html: True
		migrate-community-posts-by-others: False
		migrate-these-tags: fiction, meta
	
  __migrate__: can be True or False. Set this to True if you want the tool to copy all your journal entries to another system.
  __generate-html__: can be True or False. Set this to True if you want the tool  to make simple html files for your entries & all comments.
  __migrate-these-tags__: If you want, you can migrate *only* posts that have one of the given lists of tags. For instance, you might want to migrate your fic posts but nothing else. If you want to migrate everything, just delete this entry or leave it empty.

	[source]
	server: http://livejournal.com
	user: myusername
	password: mypassword
	
  You *must* have a source journal section. This is the journal that gets archived locally & the journal that will be optionally copied elsewhere.
 
	[destination]
	server: http://insanejournal.com
	user: myotheruser
	password: myotherpassword

  This is the destination journal; the place you're migrating to. You can leave the destination section out if you don't want to migrate.

* Proxied connections to the net

  Some workplaces use proxy servers to mediate all web traffic. If you've had to tell your web browser to use an HTTP proxy, you'll need to give ljmigrate the same information. You can omit the port. You should omit this section entirely if you don't use a proxy.
  
	[proxy]
	host: proxy-server-name
	port: 8000  
  
* Run the Terminal. It lives in Applications, in the Utilities folder.

* In the terminal, change directories to the place where you unpacked the 
  tool. Exactly where that was, is up to you & your web browser. The desktop
  is one popular location. 
  
		cd ~/Desktop/ljmigrate

  If you unpacked it somewhere else, cd to there. One way to find out where is:
  	switch to the Finder
  	look at the Finder window for the ljmigrate folder
  	command-click on the titlebar: the folder trail shows up in a popup

* Execute the tool:

		./ljmigrate.py

* Watch and wait. The tool is pretty chatty about what it's doing.

## What the tool produces

The tool makes a directory named for your account, with a subdirectory containing your userpics and one subdirectory for each entry. If you're generating html, it also makes an html directory inside the account directory, with bare-bones html versions of each of your posts, with comments.

	username/
		entry000001/
			entry.xml
			comments.xml
		entry000002/
			entry.xml
			comments.xml
		userpics/
			keyword1.jpeg
			keyword2.png
		html/
			index.html
			00001.html
			00002.html
		metadata/
			ljmigrate.log (logfile, with error reports)
			(program state, including the last time we synced)
		
If you're migrating, the posts at the destination will have all metadata preserved (user pic keywords, mood, tags, music, and location). They also have all privacy settings preserved (private, friends-only, custom filter settings). Custom friend groups are not yet migrated, since the tool makes no attempt to migrate friendlists. (That would, in my opinion, be dicey since you don't know that your friends at the destination have the same account names as your friends at your original journal site.)

The tool can't migrate your userpics, because the LiveJournal API provides no way to add or edit icons. You'll need to upload them by hand. The image files in userpics/ are named by keyword, to help. Spaces are replaced by _ (underbar) for various geeky reasons.

## Migrating communities

In your config sections for "source" and "destination", add the names of the communities you want to back up or migrate:

	[source]
	server: http://livejournal.com
	user: my_original_user
	password: the_password
	communities: pony_fans cardboard_fans comm_to_archive_and_not_move

	[destination]
	server: http://insanejournal.com
	user: my_new_user
	password: the_password
	communities: pony_asylum cardboard_mania 

All three communities will be archived, and these migrations will happen:
	pony_fans => pony_asylum
	cardboard_fans => cardboard_mania
	comm_to_archive_and_not_move not migrated

NOTE! You'll be the poster of any migrated community entries. By default the script will migrate ONLY your own posts. If you want to migrate everything and don't care that you'll be the poster on the other end, set this option:

	[settings]
	migrate-community-posts-by-others: 1
	
If you enable moving posts by other people, each migrated post that you didn't make originally will have the original poster's name added at the top.

NOTE! Moving communities has one other limitation to know in advance: community posts can't be back-dated the way regular journal posts can be. That means if you migrate a journal, every single post will appear as new in member's friendslists. This is a restriction imposed by the LJ client protocol.

There is one limitation to LJ's comment exporting script that affects how communities are archived. You must be a maintainer of the community to get access to get access to the comments. This is an artifact of how LJ's export_comments.bml script works.


## Secret nuclear option

While testing I found it very useful to have a tool that nukes all the entries in my test journal. I figured you could use it too.

Add a section to the config file that looks like this:

	[nuke]
	server: http://insanejournal.com
	user: user_to_nuke
	password: the_password

Run the tool with the --nuke flag:

	./ljmigrate.py --nuke

Answer the two questions with Y if you REALLY REALLY mean it. THERE IS NO GOING BACK ON THIS. ONLY RUN IT IF YOU REALLY WANT ALL YOUR POSTS DELETED.

## Troubleshooting

TBD

## Contributors

[don-pit (Petr Gladkiy)](https://github.com/don-pit)

Early bug-reporting provided by LJ users ldybastet, kannnichtfranz, eleraama, daiseechain, nm973, zortified, weirdquark, winterthunder, unmisha, chicken_cem.

## Changes in ljmigrate 1.5 with dreamwidth support compared to plain 1.5

- Add support for plaintext passwords over https to allow use with dreamwidth.
- Hans de Goede: note this is just a quick hack which worked to migrate my
  journal from livejournal to dreamwidth, which I hope will be useful to others
  too. I do not have time to offer support on this.

## Changes from ljdump 1.2

- Uses Configparser instead of xml for config, since we are not insane.
- The concept of object-oriented programming is introduced.
- Userpics are downloaded & image type identified.
- Html generation for posts + comments.
- Others too numerous to mention-- the codebase has now diverged seriously.
