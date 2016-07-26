
Two weeks ago I had an opportunity to attend PyGotham conference at the United Nations as a part of Open Camps initiative. Thank you [Women in Machine Learning and Data Science (WiMLDS)](http://wimlds.org/) organization for giving me this opportunity. I was very excited to meet so many people passionate about Data Science and Python development.
{: style="text-align: justify"}


As new to Python community I was pleased to see talks oriented for beginner and professional audience, from techniques on cleaning data with Pandas and using BeautifulSoup 4 for webscraping to Microservices with Nameko and Tensor Flow projects. Among other talks I enjoyed two lectures given by [Magnetic](http://www.magnetic.com/), a company that provides data driven marketing solutions for clients, where Julian Berman discussed [PyPy](http://pypy.org/) and Jonathan Arfa discussed [algorithms for getting samples from streams](http://tech.magnetic.com/2016/04/virbs-sampling-events-from-streams.html)
{: style="text-align: justify"}


It turned out it was Julian Berman's first talk at a U.S. based conference, most of his presentations were done in Europe. He started from basics explaining the architecture of Python: from Python syntax parser to bytecode, to stackable CPython Virtual machine, and how large scale deployments are lacking speed without using a compiler. Available solutions such as C API, Cython, IPC that are highly compatible with CPython have their pluses and minuses but require manipulating both C and Python code, while PyPy and PyPy Just-In-Time(JIT) compiler are written in RPython. They are implemented using the same language, they are still compatible with CPython based code and in some cases perform several times faster that previously mentioned C flavored compilers. I was sold. Off to the next lecture.
{: style="text-align: justify"}


A problem of obtaining a sample from streams of data is highly important in electronic trading and advertisement bidding. When data is coming every second there are no resources to store incoming information, and batch algorithms perform poorly. The common practice is to constantly keep a sample of fixed size k events to perform analytics in real time. Jonathan Arfa carefully explained the existing Reservoir Sampling algorithm(https://en.wikipedia.org/wiki/Reservoir_sampling) which produces a uniform over the time sample. These samples are not useful if the rate of events changes fast, like afternoon web traffic. In this case you do not want an unbiased sample, so Magnetic has done some research and came up with Variable Incoming Rate Biased Samplers, where the specified parameters are the desired size of the sample and desired mean age of events in the sample. The full set of slides is available at [this link](http://il.pycon.org/2016/static/sessions/jonathan-arfa.pdf).
{: style="text-align: justify"}
