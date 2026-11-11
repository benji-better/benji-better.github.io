---
layout: page
title: Airline Dashboard with Live Updates 
description: Live Cancellation Dashboard with Airport Cancellation Map
img: assets/img/airport_cancel_img.png
importance: 1
category: work
giscus_comments: true
---

### Highlights
* Constructed a simulated real-time flight operations pipeline processing historical flight data (2M+ records) to model live domestic flight disruptions.
* Engineered time-series data simulation scripts using Pandas to segment massive historical datasets into sequential, 10-minute micro-batches to replicate operations data streams.
* Built an interactive live-mapping application using Plotly Dash and custom callback logic to visualize cancellations as geographic hotspots.
* Developed dynamic multi-airline tracking metrics comparing cancellations across the big four domestic airlines (Southwest, American, Delta, United) to deliver insights within a live updating carrier performance dashboard

[repository link](https://github.com/benji-better/airline_dashboard)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/skysolver_prof_pic1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/cancel_by_airline_img.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/airport_cancel_img.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

This project is an interactive, simulated real-time data streaming dashboard built to track and visualize airline operational disruptions. 

Using Python, Plotly Dash, and Pandas, the application simulates live-streaming data to monitor flight cancellations and support rapid resource reallocation during large-scale logistics bottlenecks.

The dashboard simulates data from December 27, 2022—the peak of the historic Southwest Airlines operational crisis—updating at 10-minute intervals to facilitate supply chain and operational corrections.

**Tools Used:** Plotly Dash, Python, Pandas
**Techniques:** Geospatial dashboard visualizations, segmentation of massive data



...
<iframe width="560" height="315" src="https://www.youtube.com/embed/S7I8NCvVmm8?si=KuysShw_1YnqgsKB" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The first dash page above is a simulated live map showing Southwest’s cancellations at airports within the US, since the beginning of the day at midnight (EST). When an airport has more cancellations, the dot above the airport grows larger.

This dashboard shows simulated live data specifically from 12AM EST of the 27th of December, 2022 until 11:50 PM EST of the same day with updates happening every ten minutes.

...
<iframe width="560" height="315" src="https://www.youtube.com/embed/jopt25o935s?si=V9rX5JB65A9EdRZq" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The second dash page above is a live chart showing a simulated real-time comparison of flight cancellations since the beginning of the day at midnight (EST) between the big four major airlines within the United States: Southwest, American, Delta, & United.

**Structure/ Data**
The dashboard comprises two live charts. These charts use a callback feature within Plotly Dash that can be connected to an API to show live status of flights across the country. Pandas was used to create raw CSV files for every ten minute period on 12/27/2022 for both charts. The data used in this dashboard comes from a Department of Transportation dataset of all domestic commercial flights from 2019 until August 2023.

**The dataset here:** kaggle.com/datasets/patrickzel/flight-delay-and-cancellation-dataset-2019-2023/

The method I used to create these live charts comes from this video: https://www.youtube.com/watch?v=37Zj955LFT0
