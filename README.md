
# About ♊️ GemiNews 📰

This is a News Slurper that takes News in real time and - hopefully - feeds an LLM with RAG knowledge.

* Session: https://sessionize.com/app/speaker/session/621013
* Slides: TODO

## Description

How can we get an LLM to be updated to today’s news?
Gen AI is great at answering questions.. from the past. After the LLM was trained, all you can do is RAG.
How about crawling the web for latest news with Gemini for multimodal extraction and offering summarization by your favorite topic?
It all gets more exciting thanks to Andrei’s langchainrb gem.

## Other Ideas

My idea is to bring slides and a demo, all done in ruby leveraging nokogiri, langchainrb and possibly some capabilities in Langchainrb that Andrei is now building (*).

Slides: explain the overall idea, empathise with audience, show architecture diagram, why we’re here, and make people laugh.

My idea is to build a demo in two parts:
A crawler which crawls a few sample web pages, extract information using Gen AI to understand if they’re pertinent to certain topics (eg music, sport, politics, ..) and extract other information (eg Location).
Then, RAG-style, I’d feed an LLM and ask questions real time hoping to be able to surprise people with last-week news about different news sections. Like:
“How are presidential elections going? What’s the latest news?”
What’s latest with the ruby community?
.. hoping to retrieve very latest news.
Possibly, retrieve similar pictures/articles based on the questions (embedding style).

## App info

### Google Cloud info

Apps are on Cloud Run

* DEV: https://gemini-news-crawler-dev-x42ijqglgq-ew.a.run.app/pages/stats
* PROD: [🫀 health check PROD](https://gemini-news-crawler-prod-x42ijqglgq-ew.a.run.app/up)

### TODOs

*  add Devise for user mgmt
*  add Cloud Run IAP: https://blog.cloud66.com/authenticating_users_with_google_iap_in_rails
* Auto feed continuously. Currently

### Autofeed now

1. `cd crawler/ ; $ make crawl-a-lot` or `make crawl-continuously` (`bundle install` if its the first time). This populates XML every 15min (or I get kicked out by the robots :P ) and slurps articles from XML. XML I check on git, articles i dont or theyre too many.
2. `cd webapp ; bundle exec make seed-forever` (without bundle wont work). this seeds info from (1) into ActiveRecord, hence DB. Make sure the env vars are NOT empty in the 🧡🧡🧡 stanza.
3. call an async routing to populate - although since v0.1.5 this should happen automatically before save of Article.
4. This workED: `cd webapp ; echo Article.compute_embeddings_for_all max_instances: 5 | rails c`. Note: since I moved from Array to Vector this script is now BROKEN.
5. On 12may24 I found the bug. When I assign the `title_embedding` it converts from float to String and before the assignment its a float! So it looks like the DB has a strong. But the DB says it has a vector, and the other Two fields have NO PROBLEMS! Maybe I should reset the DB and see what happens..
6. Bug fixed by adding this to the model:
```
  has_neighbors :article_embedding
  has_neighbors :title_embedding
  has_neighbors :summary_embedding
```
Damn it was THAT easy.
Error:
```
(NoMethodError)

      value = value.to_a.map(&:to_f)
                   ^^^^^
Did you mean?  to_s
               to_d
               to_f
               to_c
               to_r
               to_i
Issue with keys
```
* Created secret: `projects/272932496670/secrets/geminews-key`
* Mounted on CRun as /geminews-key/geminews-key
* Now the final bit: GCP_KEY_PATH_FROM_WEBAPP = /geminews-key/geminews-key

### esbuild issues

This didnt work:
* `rails-ric webapp`
* this didnt work. Will do again with tailwind but without esbuild
* rails new "$1" -j esbuild --css tailwind

Lets try a second time🧮

```bash
rails new webapp --database=postgresql --css tailwind
rm -rf webapp/.git
git add webapp
```

* `rails-which-javascript-env ` should say ESBUILD since i installed it with it :)

## Nice to haves

* Add Gemini function calling for getting News.
