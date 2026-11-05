Trafilatura removes most of the structural noise, but not everything, so keep a light cleanup in refine as a safety net.

What trafilatura does at the scrape stage

This assumes it gets the full HTML, ideally as raw bytes (fetch_url or r.content), not a string that was already decoded.

Noise	Trafilatura at scrape time
Mojibake (â€™, Ã©, â€¢)	Prevented, not repaired. With bytes it detects the charset before decoding. If you hand it a string that is already wrong (like your CSV text), it leaves the damage in place.
HTML entities (&amp;, &nbsp;, &rsquo;)	Removed. The HTML parser turns them into real characters. Double-encoded ones (&amp;amp;) can survive as &amp;.
HTML tags (<p>, <div>, <br>)	Removed. The output is text (paragraphs separated by line breaks).
Extra spaces and newlines	Mostly. Whitespace is normalized, but some odd spacing can remain.
Nav text, cookie banners, “Share this”	Mostly. This is its main strength, but it is heuristic. Share prompts or “Also read” lines placed inside the article body can stay, and so can a cookie notice injected into the main container.
Duplicated sentences	Partly. deduplicate=True drops text segments it has already seen, using a cache across calls in the same run. I described this earlier as removing repeats “inside the page”, which was not accurate. It can also drop legitimate repeated sentences (wire copy shared between articles), so test it before relying on it.
Zero-width and control characters	Partly. Invalid control characters are usually dropped, but zero-width spaces and similar can remain.
Other effects to expect
Non-article pages return None (galleries, live blogs, pages with little text). That works as an early filter.
Short articles can be dropped as too small. If you lose real short news, try favor_recall=True or lower the minimum size setting for those sources.
It can remove real content. It is a heuristic, so check a sample of extracted text against the page, especially for sites with unusual layouts.
Recommended setup: layered
Scraper: trafilatura extracts the body from bytes. This removes the bulk of the tags, nav, cookie banners and ads, and avoids most mojibake.
Refine (light): a small safety net that takes milliseconds:
python
import html, re
from ftfy import fix_text

ZERO_WIDTH = re.compile(r"[\u200b-\u200f\u202a-\u202e\u2060\ufeff]")

def safety_net(text) -> str:
    if not isinstance(text, str):
        return ""
    text = html.unescape(text)          # leftover or double-encoded entities
    text = fix_text(text)               # any mojibake that still got through
    text = ZERO_WIDTH.sub("", text)
    return re.sub(r"\s+", " ", text).strip()
Refine (patterns): keep a short BOILERPLATE list and the duplicate check for what trafilatura missed. The audit script from earlier shows what that is, and the list will be much shorter than it is today.

-------------------------------------------------------------


I am creating csvs from 
raw news (title , summary, full_article, date, source etc ) then catgorize ( newsCatgory, eventIdentifed)

now my goal is to link news under the same event 

On the UI want to show varcity of a event:  
event heading below the news related to event along with the source 

currently pushing the full csv raw + catgorize columns combined in OpenSerach 

Will need to use Opensearch to fullfill the Varacity or else and if Openserach how the FastAPI endpoint fetches from OpenSearch

how this works i ahave noidea of Opensearch working&#x20;



how these diffrent are stored together ?



# 2. How to link news under the same event

Consider these three articles:

|   |
| - |

Source

|   |
| - |

Headline

|   |
| - |

BBC

|   |
| - |

Explosion reported in Islamabad

|   |
| - |

Reuters

|   |
| - |

Blast damages building in Pakistan's capital

|   |
| - |

Dawn

|   |
| - |

Rescue teams respond to Islamabad explosion

They have different headlines, but potentially report the same incident.

You should not rely on exact headline matching.

Instead, use a combination of:

1. Text similarity.
2. Semantic similarity.
3. Named entities (people, organizations, locations).
4. Event date/time.
5. News category.
6. Event-specific context.



does any LLM is involved ?
