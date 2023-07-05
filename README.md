# A Search Tool for Lumen

1. [Introduction](#intro)
2. [Modes](#modes)
3. [Main variables](#variables)
4. [Field Names](#fieldnames)
5. [Output](#output)
6. [Useful links](#links)
7. [Credits](#credits)

## Introduction<a name="intro"></a>

This repository contains a search tool designed to mine data from the Lumen database.

It was developed for the [Latin American Center for Investigative Journalism](https://elclip.org/), with the support of the [International Center For Journalism](https://www.icfj.org/our-work/disarming-disinformation-empowering-truth) and the [Scripps Howard Foundation](https://scripps.com/fund/).

An authentication key is needed to access to the Lumen API. To request one, you can write to [team@lumendatabase.org](mailto:team@lumendatabase.org)

Scope of the tool is to iterate three different types of searches that, starting from a suspicious-looking DMCA takedown request, will extract the network of connected sites likely administered by the same actor. Because the Lumen API doesn't have a specific parameter for certain fields (such as `copyrighted_urls` and `infringing_urls`) search may result in noisy data, especially if it includes generic words that may be found in the `description` field. For this reason, during every search it's better to keep an eye on the terminal.

If you notice the search is looking for urls that are too generic or too far away from the starting point, stop the process and review the output.csv file to see when the search took the wrong path. The field `searched_url` should help find the problematic url, so that you can add it to the `to_skip` lists.

To limit the amount of noise (but, also, of data) the function `SAVE_ONLY_BAD_URLS` should stay on `True`.

## 1) Modes<a name="modes"></a>

The tool work in three different modes:

1. **MODE 1** : search by specific url or other key words
2. **MODE 2** : search by infringing_urls
3. **MODE 3** : search by copyright_urls from blogging platforms

MODE 1 is the starting point and the first search we are going to make. Add the desired url(s) to the list.
MODE 2 looks for notices that include the already fetched `infringing_urls` value.
MODE 3 looks for notices that include the already fetched `copyright_urls` value, but expressed in its source format (ex. from "fraudulentDMCA.blogpot.com/article-about-nasty-person" search for "fraudulentDMCA.blogpot.com)".

Once you started in MODE 1, you should me able to run MODE 2 and MODE 3 in an alternate way until you getting no more results and a cluster is identified.

Mode can be changed by changing the value (1, 2 or 3) in the `GET_URL` variable.

## 2) Main variables <a name="variables"></a>

* **type_search** : on the Lumen API there is no specific parameter for urls. Hence, we rely on the parameter `type_search` for our searches. Note that this may result in unwanted data if the searched url have very generic words, as they may be found in other fields (such as description).
* **init_url** : specific url, list of urls or other key words that are used in search mode 1.
* **to_skip** : specific url, list of urls or other key words to avoid searching. Add new entries if you realize the script is fetching wrong data. Despite the `term-require-all` parameter, some urls with very generic names (ex. news-breaking.news.blog) might create partial matches with a lot of results, and ruin our data.
* **bad_words** : words used to identify blogging platforms. These words are used in `MODE 2` to find copyright urls that are registered in blogging platform and extract the source origin (for example, from "http://world-newshub.blogspot.co.il/2012/05/israeli-fugitive-caught.html" to "http://world-newshub.blogspot.co.il"). Right now, the words are: `blogspot` `livejournal` `issuu` `weebly` `wordpress` `tumblr` `over-blog` `food.blog`.
  
* **SAVE_ONLY_BAD_URLS** : By default, the variable `SAVE_ONLY_BAD_URLS = True`. This was introduced to limit the noise that created by long sets of searches. In fact, partial matches might result in fetching of data that is not of our interest. This function limits the saving to only those notices that include as their Infringing Url a blogging platform, recognized if it include a one of the terms included in the `bad_words` list.

## 3) Field Names <a name="fieldnames"></a>

This tool will extract notice information that include:
* **id** : the unique ID number in the Lumen dataset.
* **type** : the type of notice. This field should always be DMCA, but it is included as a control tool.
* **date_received** : the date in which Google received the DMCA notice, expressed in yyyy-mm-dd.
* **sender_name** : the name of the person or organization that presented the DMCA notice, as filled out by the author of the notice.
* **jursdictions** : the jurisdiction for the DMCA notice, as filled out by the author of the notice.
* **copyrighted_urls** : the url allegedly owning ownership over the content published by the infringing_url. If more than one, values are separated my semi columns.
* **infringing_urls** : the url of the content allegedly stolen from the copyrighted_url. If more than one, values are separated my semi columns.

## 4) Output <a name="output"></a>

The script results in the creation of a number of files in the directory:

* **output.csv** : is a .csv file containing the data fetched from the Lumen database. It includes eight columns corresponding to the `fieldnames` values.
* **read_urls.pkl** : a python pickle file containing information about the urls ot other words searched so far. Once stored there, a url or other word will not result in a new search, regardless on whether it is included among the ones to be searched in MODE 2 or it was automatically detected in MODE 2 or MODE 3.
* **infringing.pkl** : a python pickle file containing information about the `infringing_urls` fields found so far.
* **bad_urls.pkl** : a python pickle file containing information about the blogging platforms found in the `copyrighted_urls` field.

## 5) Useful reads <a name="links"></a>
#### **Legal and academic**
* *[The Digital Millennium Act of 1998](https://www.copyright.gov/legislation/dmca.pdf)*
* Court of Justice of the European Union, *[Document 62012CJ0131, Judgment of the Court](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A62012CJ0131)*, May 13, 2014
* Court of Justice of the European Union, *[Press Release No 70/14](https://curia.europa.eu/jcms/upload/docs/application/pdf/2014-05/cp140070en.pdf)*, May 13, 2014
* Court of Justice of the European Union, *[Press Release No 112/19](https://curia.europa.eu/jcms/upload/docs/application/pdf/2019-09/cp190112en.pdf)*, September 24, 2019
* UC Berkley, [Notice and Takedown in Everyday Practice](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2755628), March 24, 2017

#### **In the news**
* OCCRP - *[Fake Copyright Complaints Seek to Remove Reports on Minister and Lawyer](https://www.occrp.org/en/daily/17370-fake-copyright-complaints-seek-to-remove-reports-on-minister-and-lawyer)*
* Torrent Freak - *[‘Elon Musk’ Sends Hundreds of Takedown Requests to Protect Precious Memes](https://torrentfreak.com/elon-musk-sends-hundreds-of-takedown-requests-to-protect-precious-memes-230127/)*, January 27, 2023
* Forbidden Stories - *[The Gravediggers: how Eliminalia, a Spanish reputation management firm, buries the truth](https://forbiddenstories.org/story-killers/the-gravediggers-eliminalia/)*, February 17, 2023
* Washington Post - *[Leaked files reveal reputation-management firm’s deceptive tactics](https://www.washingtonpost.com/investigations/interactive/2023/eliminalia-fake-news-misinformation)*, February 17, 2023
* Lumen - *[Over thirty thousand DMCA notices reveal an organized attempt to abuse copyright law](https://lumendatabase.org/blog_entries/over-thirty-thousand-dmca-notices-reveal-an-organized-attempt-to-abuse-copyright-law)*, April 22, 2022
* Rest Of The World - *[Exposed documents reveal how the powerful clean up their digital past using a reputation laundering firm](https://restofworld.org/2022/documents-reputation-laundering-firm-eliminalia/
)*, February 3, 2022
* Qurium - Dark Ops Uncovered *[Episode 2](https://www.qurium.org/forensics/dark-ops-undercovered-episode-ii-eliminalia-analysis-of-fake-dmca-complaints/)*, April 20, 2021
* Qurium - Dark Ops Uncovered *[Episode 1](https://www.qurium.org/forensics/dark-ops-undercovered-episode-i-eliminalia/)*, April 12, 2021
* Torrent Freak, *['Impostors' manipulate Google with fake takedown requests](https://torrentfreak.com/impostors-manipulate-google-fake-takedown-request-180805/)*, April 5,2018
* Huffington Post, *[The Dark Art of Fake DMCA Takedown Requests](https://www.huffpost.com/entry/the-dark-art-of-fake-dmca-takedown-requests_b_57a4962ae4b034b25894b63f)*, August 5, 2016

#### **Links**
* [Lumen database](https://lumendatabase.org/)
* [Lumen API documentation](https://github.com/berkmancenter/lumendatabase)
* [Google Transparency](https://transparencyreport.google.com/copyright/overview?hl=en)
* [Google Search removals due to copyright infringement FAQs](https://support.google.com/transparencyreport/answer/7347743?hl=en#zippy=%2Cwhy-do-you-delist-some-urls-but-not-others)



## Credits <a name="credits"></a>

* Davide Dalla Stella, [GitHub](https://github.com/fromstar) [dallastella.davide@gmail.com](mailto:dallastella.davide@gmail.com) 
* Marco Dalla Stella, [md3934@columbia.edu](mailto:md3934@columbia.edu)
