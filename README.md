# Qiqqa Searcher

The backend / engine used by [Qiqqa](https://github.com/jimmejardine/qiqqa-open-source) to provide full text & metadata search and index powers across all your Qiqqa libraries. Based on [the beautiful Apache SOLR™](https://solr.apache.org/).


## How 'Twas In The Olden Days

Before this, Qiqqa was using a truly *antique* copy of [Lucene.NET](https://github.com/apache/lucenenet). However, when we had a look at that project in the years 2019-2021, things did look pretty bleak from my perspective: I would need to change the entire C# interface *anyway* (thanks to that *antique copy* using the now obsolete Lucene.NET API), plus Lucene.NET is lagging behind quite a bit (equivalent to Lucene 4.x, while Apache Lucene (and SOLR) are now at v8.9.x, so that's getting pretty darn close to v9.x right there, while Lucene.NET is still in the v4's!

Then we had [a look at *activity*](https://github.com/apache/lucenenet/pulse/monthly). While the .NET repo might be active, it's nothing compared to the OG of OGs: [Apache Lucene itself](https://github.com/apache/lucene/pulse/monthly) [\[& here\]](https://gitbox.apache.org/repos/asf?p=lucene.git;a=shortlog). And thanks to ElasticSearch (which I've been considering at too) pulling some nasty licensing shenanigans, Apache [SOLR](https://github.com/apache/solr/pulse/monthly) has [picked up the pace](https://gitbox.apache.org/repos/asf?p=solr.git;a=shortlog) during Covid-2020 and the choice is clear to me (for I am no Java coding fan): SOLR it is! 

## [Apache SOLR](http://solr.apache.org/) it is!

That will give us a nice local web interface that anyone can use and thus saves me the added (future) hassle of provviding additional access for power users and specialty requests where folks would like to dig into their library content and metadata in unfathomable ways: after all, I set out doing all this in order to *open up Qiqqa* for advanced usage, so you can write your own scripts and have them do special things to/with your library that *you* need/want done. Me, I'm *one* of these users where I would dearly like to be able to do some very talk-specific work with my libraries that a generic tool like Qiqqa won't ever be able to supply with the same ease & speed of writing a bit of script, or maybe only a bunch of shell commands, to extract and process the filtered (meta-)content to suit my needs **right that moment**.

This is why using a generic and very powerful full text search engine like SOLR is a boon to all: Qiqqa can do its thing and (help) fill the SOLR database, while we can use Qiqqa for the usual search stuff quite nicely, only reverting to writing a bit of script when we have bigger/bulkier demands that a GUI is hard-pressed to deliver. 

Think: filtered summaries, augmented search data mixed together with other sources, anything you can come up with that would take longer to accomplish in a click&point user interface. Think *batch processes*. And I'm sure you'll come up with novel ideas to use and process this accessible data that I haven't thought of yet!


## Some techy/tekky details

- Qiqqa uses **content hash codes** to identify each document in the library collection. ([Qiqqa v80 series uses SHA1B (a.k.a. B0rked SHA1); *future* (at the time of this writing) Qiqqa uses BLAKE3+B58](https://github.com/jimmejardine/qiqqa-open-source/blob/master/docs-src/Progress%20in%20Development/Fingerprinting%20-%20moving%20forward%20and%20away%20from%20b0rked%20SHA1.md))

  These hash codes look some like this: `e440b55474b288f9ff5127ee3bf35c67909ec858`, and are also used to (re)name the PDF documents in each library.
  
  Meanwhile, all data fed into the SOLR search engine is linked to its docment source by using that very same hash code as it's `<UniqueKey>` in SOLR, thus enabling you to quickly identify document file(s) that are linked to the search results you'll be getting from SOLR. Handy!
  
- Another local 'web' server called **Qiqqa Docu Base**, which is part of the Qiqqa/MuPDF toolkit, harbors the Qiqqa Library Database (SQLite) and can be queried to produce more relevant info about and around a given **content hash code**... or a *bunch of those* if you're into doing *bulk operations*.

  + **Qiqqa Docu Base** is, for instance, where you may go to inquire about document copies, revisions, reprints, extracts, *bundling*, etc., i.e. any document that has been filed as a (*near-identical*) copy of this one, be it a *revision* (like you see on ArXiv: v1, v2, v3, etc.), a *reprint* (the same, or slightly altered, paper published in a different magazine), a *bundle* (this paper being part of a published book, e.g. Springer Scientific Proceedings series), etc.
  
  + You *could* ask SOLR for this information but it's often faster to obtain direct graph data like that from a database when the relations are not *fuzzy* like you would have in a *full text search* when you are looking for phrases, words or near matches thereof.
  
  + **Qiqqa Docu Base** is also the place to visit when you want to fetch/"*download*" the original PDF document(s) attached to those `<UniqqueKey>` **content hash codes**: both  subsystems act as simple, regular web servers and can be easily addressed that way, either through using simple script requests or using your generic web browser, thus offering a different UI/UX, next to the **Qiqqa Application** itself.
  
  
  





