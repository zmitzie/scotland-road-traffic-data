# Project 2: Scotland road traffic data (100 marks)

One of the ways the Department for Transport (DfT) monitors road traffic on the British road network is by periodically sending people to count the number of vehicles travelling on a given road per hour. These counts are performed each year, over a 12-hour period, on a weekday between March and October (excluding public and school holidays). Some roads are counted like this every year, some less often.

The choice of counting on a weekday between March and October is so that the number of vehicles counted on such a day can be assumed to accurately represent the number of vehicles which would be observed on an "average" day of the year. The DfT therefore uses this data to estimate the annual average daily flow (average number of vehicles per day travelling on that road, over a year) on each road.

The DfT releases this data to the public every year, under an [Open Government License](http://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/). At this time, data is available for the counts made in years 2000 until 2019. In this project, you will explore this data to gain some insights about traffic on Scottish roads over the past 20 years.

*(Note that these instructions are available in both the `project-2-instructions.md` file and the `project-2-instructions.ipynb` file. They are exactly the same -- this is just so that you can read them easily whether you're in Jupyter or not.)*

---

## 🚩 Goal and structure

The goal of this project is for you to present a **coherent and well-coded investigation** into some aspects of the dataset. You should do this in the Jupyter notebook called **`project-2-report.ipynb`**, which will contain your code, as well as any results and visualisations. You should also use Markdown cells to structure the notebook, describe your investigations, and present/explain your results.

This project is an **open-ended** task, so you can come up with your own ideas to analyse the dataset and extract useful information or interesting facts. Some ideas of questions you might address in your analysis will be given in the section "Some ideas to get you started"; they are a guide to some things you could think about to get you started, but you don't need to answer them all, and you are strongly encouraged to answer questions that are not listed.

When you are presenting your investigation of each problem, be sure to make a coherent discussion for each task (using Markdown, maths as appropriate and code cells). In particular, since you are working as a group, some work will be needed to combine all of the code and writing that you each contribute into the submitted notebook -- this is something that you should plan to do, and the quality of **presentation** will be marked.

---

## Data

The file `dft_rawcount_region_id_3.csv` contains the data for all manual counts performed on Scottish roads from 2000 to 2019.

In this data, a 'link' is the name used to refer to a particular **section** of road. A given road (i.e. a unique `road_name`) can contain multiple links. One "count point" is decided for each link (a location at which the count is made for that link), and given a unique `count_point_id`.

There are 33 columns in the data; the description of what each column represents is given here for reference. It's probably a good idea to have a look at the data first, and come back to these tables if you're not sure about one of the columns.
       
| Column name | Description |
|:--|:--|
| `count_point_id` | A unique reference number for each *link* (i.e. section of road) |
| `direction_of_travel` | Direction of travel: `N`, `S`, `E`, `W`, or `C` for 'Combined' (when the direction can't be easily determined) |
| `year` | Counts are shown for each year from 2000 onwards |
| `count_date` | The date when the actual count took place |
| `hour` | The time when the count took place, where 7 represents between 7am and 8am, and 17 represents between 5pm and 6pm |
| `region_id`, `region_name` | Website region identifier, and name of the region that the count point sits within (in the provided datafile, which is only for Scotland, these will always be `3` and `Scotland` respectively) |
| `local_authority_id`, `local_authority_name` | Website local authority identifier, and name of the local authority that the count point sits within (for instance, `Aberdeenshire`) |
| `road_name` | Road name (for instance `M9` or `A698` -- see e.g. the [green and purple labels in this map](https://www.scotlandinfo.eu/images/maps/scotroadmap1.jpg)). Note that `M..` roads are motorways, `A..` roads are major roads, `B..` roads are minor roads, and `C` and `U` roads are very small roads, which are not numbered (i.e. for example, all `C` roads have `road_name == 'C'`) |
| `road_type` | Whether the road is a `Major` or `Minor` road |
| `start_junction_road_name`, `end_junction_road_name` | The road names of the start and end junctions of the link |
| `easting`, `northing` | Easting and northing coordinates of the count point location |
| `latitude`, `longitude` | Latitude and longitude of the count point location |
| `link_length_km`, `link_length_miles` | Total length of the network road link for that CP (in kilometres and miles, resp.) |

The remaining columns all contain the observed number of vehicles for different types of vehicle:

| Column name | Vehicle description |
|:--|:--|
| `pedal_cycles` | Pedal cycles (e.g. bicycles) |
| `two_wheeled_motor_vehicles` | Two-wheeled motor vehicles (e.g. motorbikes) |
| `cars_and_taxis` | Cars and taxis |
| `buses_and_coaches` | Buses and coaches |
| `lgvs` | Light goods vans (e.g. commercial vans) |
| `hgvs_2_rigid_axle`, `hgvs_3_rigid_axle`,<br /> `hgvs_3_or_4_articulated_axle`,`hgvs_4_or_more_rigid_axle`,<br /> `hgvs_5_articulated_axle`, `hgvs_6_articulated_axle` |  Different kinds/sizes of heavy good vehicles (e.g. trucks, lorries) |
| `all_hgvs` | All heavy good vehicles combined |
| `all_motor_vehicles` | All motor vehicles combined (so all of the above, except cycles) |

#### Links

Here are all the places where the data and information come from -- feel free to take a look at these if you'd like to see how the DfT uses this data to produce different statistics.

- [Scotland datasets](https://roadtraffic.dft.gov.uk/regions/3) -- you can download the datasets at the bottom of the page. The file `dft_rawcount_region_id_3.csv` is the "Raw counts" file there.
- [All UK-wide datasets](https://roadtraffic.dft.gov.uk/downloads) -- the same data, for all of Britain (UK, excluding Northern Ireland).
- [Road traffic statistics metadata (PDF)](http://data.dft.gov.uk.s3.amazonaws.com/road-traffic/all-traffic-data-metadata.pdf) -- the note explaining how the data is structured. For example, the tables above come from Sections B-iii and C of this document.
- [Road traffic estimates in 2019](https://www.gov.uk/government/statistics/road-traffic-estimates-in-great-britain-2019) - Reports produced by the DfT using traffic data (the manual counts, together with other estimates) for the year 2019. These could give you some ideas for how to present your analysis.
- [Road traffic estimates: Methodology note](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/524848/annual-methodology-note.pdf) -- the methodology note describing how this data is used, together with further data from automatic traffic counters, to produce yearly road traffic estimates and summary statistics.
- [Motorway Database](https://www.roads.org.uk/motorway) -- Detailed information about motorways and major roads in the UK, including the different junction numbers (may be useful if you're looking at the `start_junction_road_name`, `end_junction_road_name` columns).
- [OpenStreetMap](https://www.openstreetmap.org/#map=8/56.858/-3.691) gives the M, A, and B-road numbers on the map.

You are also free to download and use more related data (from the links above or from other sources -- just make sure the data is licensed in a way which allows you to use it!) to complement your investigation if you wish.

---
## 🚩 Some ideas to get you started

Here are some of the questions you could address. Again, *you don't have to do every single one of these*, and you are certainly encouraged to come up with your own questions too. The important thing is that your report is well-presented, that your code works as intended, and that you explore some of the aspects of this dataset in reasonable depth, presenting results in a way which help you answer relevant questions or queries.

For all these questions, you can assume (like the DfT does) that you can extrapolate the vehicle count performed on one day to the average count for that year.

- Which local authorities manage the most number of different *roads* (not links)?
- Which road is the longest (assuming that every link (section) of that road is associated to a counting point in the data)? Which road has the most number of different counting points along it?
- Excluding C-roads and U-roads, which have been the 5 busiest *roads* on average over the past 20 years, in terms of number of vehicles per day? Is the answer different for different types of vehicles?
- On an average day, what is the distribution of the different types of vehicles on different categories of road (M, A, B, C, U roads)? Has this changed significantly over the years?
- The DfT produces [these graphs](https://roadtraffic.dft.gov.uk/regions/3) using the manual count data, together with data from automatic traffic counters (which is not released), to calculate annual average daily flows (AADF, average number of vehicles travelling per day in a given year) for each link, and multiplying this by the length of the link to get a total *traffic volume* for the whole country, in vehicle-miles (or vehicle-kilometres). Using only the manual count data, can you reproduce similar graphs? For instance, you could try to interpolate/extrapolate data for missing years.
- What is the most likely time of the day to find a bicycle on a C-road? What are the most likely weeks of the year to find a bicycle anywhere?
- Can you identify patterns in vehicle counts depending on the time of the day?
- Look on [a road map](https://www.openstreetmap.org/#map=8/56.858/-3.691) for the major roads coming into Edinburgh (for example, find the A roads joining the City Bypass). These roads are taken, for example, by people living outside the city and driving to work. Can you identify patterns of traffic on these roads depending on the time of day and direction of travel?
- To a rough approximation, the border between Scotland and England almost forms a straight line between coordinates (latitude, longitude) $(55.0, -3.0)$ and $(55.81, -2.03)$. Using this information (or another method), find the closest count points to the border, and estimate, on average, how many heavy goods vehicles cross the border every day.
- Think about how you could map or visualise the roads/places where people most use bikes, where most buses and coaches drive, where the biggest trucks can and can't drive in or around big cities, etc...
- You can also choose one or a small number of roads, and focus your investigations on this particular part of the network.
- Can you help the DfT decide where they should send counters next year?

---

### 🚩 Working on your project

You should meet with your group **as soon as possible** when the project is released. During this meeting, **edit the file `group_plan.md`** to put your plan down in writing, and **push** the changes back to the GitHub repo when you're done.

**We will check** your `group_plan.md` at the end of Week 9, to check that every group has met and agreed on a plan. You can think of it as a "contract" between yourselves, to ensure that everyone's expected contribution to the project is clearly outlined from the start.

Feel free to keep updating that file as you work on the project, e.g. to keep track of the different tasks, who you've agreed is responsible for them, and their progress.

- We strongly recommend that you **pair-program** as much as possible! You will likely be much more productive if you have another person to bounce ideas off of about what to investigate or how to present results, and help each other solve problems. You can do this in pairs, you can mix up pairs, you can even do "group programming" sessions with one driver and 2-3 navigators if that's helpful.
- You could also schedule quick **code reviews** for each other, to help each other stay on track and write better code.
- Use your shared GitHub repo to **collaborate**. Every time you start working on the project, start by **pulling** the latest version from GitHub. Then, as you work, **commit** your changes regularly. When you are done for the day (or even before that), **push** your work to the GitHub repo to share it with your team.
- To **submit** your project, all you need to do is have your final version (ready for submission) **pushed to your GitHub repo**. At the deadline, we will clone all repos with `pp-project-2` in the name to collect your submissions.
- For that reason, **do not rename your repo** on GitHub! If you've renamed your repo and we cannot clone it at the deadline, you will be liable for **late penalties**.

---
### Packages

You can use any package or library that you find convenient. From those we've seen in the course, you will probably find **pandas**, **seaborn**, **Numpy**, and **matplotlib** particularly useful. SciPy could also provide useful computational tools, depending on where you want to take your project.

If you use any packages which do *not* come pre-installed with Anaconda, then you should provide **installation instructions**, so that the markers can easily install these packages to run your code and mark your work.

If you choose to visualise anything on a map (e.g. using latitudes/longitudes of CPs, or using the road network maps provided [here](https://roadtraffic.dft.gov.uk/downloads) as shape files), packages like [geopandas](https://geopandas.org/) or [folium](https://python-visualization.github.io/folium/) might be useful.

If you fancy making interactive visualisations, with things like toggle buttons or sliders, you could look into [IPython widgets](https://ipywidgets.readthedocs.io/en/latest/examples/Widget%20Basics.html). (This is how the "Reveal solutions" buttons are made in your tutorial notebooks, for example.)

---

### Providing references

Most of the content of your submission must be **authored by your group**. That being said, you may use any code from the course material (e.g. workshop tasks, tutorial sheets, videos), without citing it.

You may also use **small pieces of code** (a few lines max at a time) that you found elsewhere -- e.g. examples from the documentation, a textbook, forums, blogs, etc... You may use this code *verbatim* (i.e. almost exactly as you found it), or adapt it to write your own solution.

A programming assignment is just like any other academic assignment -- and therefore, **you must provide a citation for any such code**, whether you use it *verbatim* or adapt it. To do so, include a code comment at the start of your script or notebook cell, indicating:
- the line numbers where the code was used or adapted,
- the URL of the source (or, if it's from a book, a full reference to the book),
- the date you accessed the source,
- the author of the code (if the information is available).

You can use this template -- delete one of the URL or book reference lines as appropriate:
```python
# Lines X-Y: Author Name
# URL: http://...
# Book Title, year published, page number.
# Accessed on 31 Nov 2020.
```

You must also provide **detailed code comments** for any such code, in your own words, to demonstrate that you fully understand how it works -- you will lose marks if you use external code without explaining it, even if it's cited correctly.

Remember to exercise caution if you use any code from external sources -- there are a lot of blogs and forums out there with very bad code! I'd recommend that you review the Week 4 video on searching the documentation.

Of course, any **non-code sources** that you used for any of your work should also be cited appropriately. You can have a "**References**" section at the end of the notebook, in a Markdown cell, to give your list of references for such sources.

With all that, we trust that you'll be able to use your best judgement, and to cite your sources appropriately -- if anything is not clear, please do ask. Note that **all submissions** will be automatically checked (and manually reviewed) for plagiarism and collusion (between groups), and [the University's academic misconduct policy](https://www.ed.ac.uk/academic-services/staff/discipline/academic-misconduct) applies.
