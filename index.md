---
layout: workshop      # DON'T CHANGE THIS.
# More detailed instructions (including how to fill these variables for an
# online workshop) are available at
# https://carpentries.github.io/workshop-template/customization/index.html
venue: "Wageningen University & Research"        # brief name of the institution that hosts the workshop without address (e.g., "Euphoric State University")
address: "20 April B3015 in Orion, 21 April B0656 in Forum, campus WUR"     # full street address of workshop (e.g., "Room A, 123 Forth Street, Blimingen, Euphoria"), videoconferencing URL, or 'online'
country: "nl"      # lowercase two-letter ISO country code such as "fr" (see https://en.wikipedia.org/wiki/ISO_3166-1#Current_codes) for the institution that hosts the workshop
language: "en"     # lowercase two-letter ISO language code such as "fr" (see https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) for the workshop
latitude: "51.985136"        # decimal latitude of workshop venue (use https://www.latlong.net/)
longitude: "5.666517"       # decimal longitude of the workshop venue (use https://www.latlong.net)
humandate: "April 20-21, 2023"    # human-readable dates for the workshop (e.g., "Feb 17-18, 2020")
humantime: "09:00-17:00"    # human-readable times for the workshop e.g., "9:00 am - 4:30 pm CEST (7:00 am - 2:30 pm UTC)"
startdate: 2023-04-20      # machine-readable start date for the workshop in YYYY-MM-DD format like 2015-01-01
enddate: 2023-04-21        # machine-readable end date for the workshop in YYYY-MM-DD format like 2015-01-02
instructor: ["Sven Warris"] # boxed, comma-separated list of instructors' names as strings, like ["Kay McNulty", "Betty Jennings", "Betty Snyder"]
helper: ["Christina Papastolopoulou"]     # boxed, comma-separated list of helpers' names, like ["Marlyn Wescoff", "Fran Bilas", "Ruth Lichterman"]
email: ["sven.warris@wur.nl"]    # boxed, comma-separated list of contact email addresses for the host, lead instructor, or whoever else is handling questions, like ["marlyn.wescoff@example.org", "fran.bilas@example.org", "ruth.lichterman@example.org"]
collaborative_notes:  # optional: URL for the workshop collaborative notes, e.g. an Etherpad or Google Docs document (e.g., https://pad.carpentries.org/2015-01-01-euphoria)
eventbrite:           # optional: alphanumeric key for Eventbrite registration, e.g., "1234567890AB" (if Eventbrite is being used)
---

{% comment %} See instructions in the comments below for how to edit specific sections of this workshop template. {% endcomment %}

{% comment %}
HEADER

Edit the values in the block above to be appropriate for your workshop.
If the value is not 'true', 'false', 'null', or a number, please use
double quotation marks around the value, unless specified otherwise.
And run 'make workshop-check' *before* committing to make sure that changes are good.
{% endcomment %}

<h4>Wageningen University & Research are pleased to invite you to <strong>{{site.title}}</strong>.</h4>

<h2 id="general">General Information</h2>

In this two day workshop you will learn about two most common long read sequencing technologies: Pacific Bioscience an Oxford Nanopore Technology. You will learn how to work with the data, perform assembly, mapping, and compare the results of both technologies.

<p id="who">
  <strong>Who:</strong>
  The workshop is intended for bioinformaticians or researchers who want to learn about application of latest sequencing technologies in genomics. 
  <strong>No prior experience with PacBio or ONT sequencing data is required.</strong>
</p>

{% comment %}
  LOCATION

  This block displays the address and links to maps showing directions
  if the latitude and longitude of the workshop have been set.  You
  can use https://itouchmap.com/latlong.html to find the lat/long of an
  address.
{% endcomment %}
{% if page.latlng %}
<p id="where">
  <strong>Where:</strong>
  {{page.address}}.
  Get directions with
  <a href="//www.openstreetmap.org/?mlat={{page.latlng | replace:',','&mlon='}}&zoom=16">OpenStreetMap</a>
  or
  <a href="//maps.google.com/maps?q={{page.latlng}}">Google Maps</a>.
</p>
{% endif %}


{% comment %}
  SPECIAL REQUIREMENTS

  Modify the block below if there are any special requirements.
{% endcomment %}
<p id="requirements">
  <strong>Requirements:</strong> It is <b>necessary to bring a laptop</b>.  A VMWare-based virtual machine with all data and tools will be provided. Basic knowledge of bioinformatics and genomics is a prerequisite. Participants will work in a Linux environment. Please check the <a href="{{site.workshop_site}}02-setup">Setup section</a> to see which tools you need to install when using your own laptop.
</p>


{% comment %}
  CONTACT EMAIL ADDRESS

  Display the contact email address set in the configuration file.
{% endcomment %}
<p id="contact">
  <strong>Contact</strong>:
  Please email
  {% if page.email %}
    {% for email in page.email %}
      {% if forloop.last and page.email.size > 1 %}
        or
      {% else %}
        {% unless forloop.first %}
        ,
        {% endunless %}
      {% endif %}
      <a href='mailto:{{email}}'>{{email}}</a>
    {% endfor %}
  {% else %}
    to-be-announced
  {% endif %}
  for more information.
</p>

<hr/>



{% comment %}
  SCHEDULE

  Show the workshop's schedule.  Edit the items and times in the table
  to match your plans.  You may also want to change 'Day 1' and 'Day
  2' to be actual dates or days of the week.
{% endcomment %}
<h2 id="schedule">Schedule</h2>

{% include wur/schedule.html %}


<hr/>


{% comment %}
SETUP

Delete irrelevant sections from the setup instructions.  Each
section is inside a 'div' without any classes to make the beginning
and end easier to find.

This is the other place where people frequently make mistakes, so
please preview your site before committing, and make sure to run
'tools/check' as well.
{% endcomment %}


