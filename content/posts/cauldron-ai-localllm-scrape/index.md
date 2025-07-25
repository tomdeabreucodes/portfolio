+++
title = 'Cauldron AI: Web Scraper Powered by LocalLLM'
date = 2025-04-25
tags = ['Python', 'LLM', 'AI', 'LocalLLM', "Ollama", "Tkinter"]
draft = false
+++

## Demo

{{< youtube 4qs5jq-zz30 >}}

## Cauldron AI in a nutshell

Cauldron is a GUI based desktop application which uses a blend of traditional scraping techniques with LocalLLM to extract useful information from websites.

## The Motivation

Sometimes you may want to run ad hoc scraping jobs on a relatively small scale to make things more efficient than manual research. 

Often I'm doing this for small hobby projects and can't justify outsourcing such jobs to Fiverr, or paying a premium for a cloud-based scraping platform.

Also, I'm a tinkerer. I'm happy to spend some time up front trying to automate a process so I can do less of the mindless work later. Traditional scraping works well, but for my use case, even if I may be looking for similar types of information, I'm often sourcing it from a wide range of different websites which have no consistent structure.

## The idea

I'd recently came across the concept of LocalLLMs and Ollama and the seed was planted... can I grab the content of my target webpage, then use LLM to mine the information I need and capture it in a structured format, say a CSV.

The answer: yes, but with some caveats.

## The implementation

Partly to challenge myself and try something new I decided to make this a desktop app, using Tkinter (Python's de facto GUI library). 
- The GUI consisted of a simple 3 stage configuration, which would determine the scraping job which was ultimately run.
- Beautiful soup 4 to grab the HTML content
- Passed it into Qwen 2.5 via Ollama with a carefully crafted system prompt
- The output was stored in a table and once it had iterated through all of the websites, the final table was exported to a CSV

## The problems and my workarounds / thoughts

Some of the key issues I faced include:

- Large webpages exceeding the context window. This would break the system prompt and cause the data to be returned in the wrong format
  - Solution: Increase the context window as far as possible while trying to avoid response degradation.
  - For a more robust solution, I could look to chunk the pages when it exceeded the maximum context size. This was overkill for my use case and I decided I can accept errors on those pages (this was not a super frequent problem for the sorts of websites I was interested in)
- The exact webpage not containing the target data
  - Solution: a smart search mode, which first checked the available links from the nav bar, and used an initial prompt to check the 3 most likely pages to contain each item of target data
  - In a more robust solution, I would have used the sitemap to more reliably check the available pages. But this was overkill for my use case
- Excessively long time to run a large number of websites using the most complex settings
  - This was ultimately the trade off of using my LocalLLM solution, so while I could optimise it further, I would always be bottlenecked by the LLM approach. This can be somewhat offset by using the right mode for certain types of jobs
    - The exact webpage mode worked very efficiently. The smart search mode described above, was MUCH more thorough, but would exponentially increase the runtime
    - For large batches exact webpage was feasible. For small batches requiring more thorough searches, smart search was more effective

## Conclusion

This was a great project to learn about LocalLLMs; packaging a local python application with large dependencies; desktop GUI design; and much more. The use case for Cauldron AI in its current state is fairly limited, but I can see it being useful for the odd bit of niche research/scraping in the future.

It was also interesting to learn about proxies and how to responsibly crawl websites, ensuring to check robots.txt and honour them. I decided not to open source this particular project due to the risk of it being modified and used for nefarious means.