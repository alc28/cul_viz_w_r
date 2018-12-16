---
title: "CNA Staffing at Beechtree Center for Rehabilitation and Nursing, Ithaca, NY"
author: "Adam Chandler"
date: "12/18/2018"
output: 
  html_document: 
    keep_md: yes
---




```r
# load libraries

library(tidyverse)
library(lubridate)
library(stringr)
library(webshot)
```


```r
webshot("http://beechtreecenter.com/",
        "data/beechtree_website.jpg",
        delay = 1.0)
```

![](presentation_2_files/figure-html/unnamed-chunk-2-1.jpg)<!-- -->


## Example Beectree staff posting

<img src="data/20180403_173039.jpg" width="1000">  



```r
# shift 0600 - 1400
90 / 10
```

```
## [1] 9
```

```r
# shift 1400  - 2200
90 / 12
```

```
## [1] 7.5
```

```r
# 2200 - 0600
90 / 5
```

```
## [1] 18
```

```r
# average across 3 shifts
90 / ((10 + 12 + 5) / 3)
```

```
## [1] 10
```


# 42 CFR 483.30 - Nursing services.

(e) Nurse staffing information  

(1) Data requirements. The facility must post the following information on a daily basis:  
(i) Facility name.  
(ii) The current date.  
(iii) The total number and the actual hours worked by the following categories of licensed and unlicensed nursing staff directly responsible for resident care per shift: (A) Registered nurses. (B) Licensed practical nurses or licensed vocational nurses (as defined under State law). (C) Certified nurse aides.  
(iv) Resident census.  

(2) Posting requirements.  
(i) The facility must post the nurse staffing data specified in paragraph (e)(1) of this section on a daily basis at the beginning of each shift.  
(ii) Data must be posted as follows: (A) Clear and readable format. (B) In a prominent place readily accessible to residents and visitors.  

(3) Public access to posted nurse staffing data. The facility must, upon oral or written request, make nurse staffing data available to the public for review at a cost not to exceed the community standard.

(4) Facility data retention requirements. The facility must maintain the posted daily nurse staffing data for a minimum of 18 months, or as required by State law, whichever is greater.  

source: https://www.gpo.gov/fdsys/granule/CFR-2011-title42-vol5/CFR-2011-title42-vol5-sec483-30 




## Centers for Medicare & Medicaid Services (CMS) Data


```r
webshot("http://cms.gov",
        "data/cms_gov.png",
        delay = 1.0,
        cliprect = "viewport")
```

![](presentation_2_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
# webshot("http://data.cms.gov", "data/data_cms_gov.png", delay = 0.5)
```


### Payroll-Based Journal (PBJ) staffing data submitted by long term care facilities 

https://data.cms.gov/browse?q=pbj&sortBy=relevance  


```r
webshot("https://data.cms.gov/Special-Programs-Initiatives-Long-Term-Care-Facili/PBJ-Daily-Nurse-Staffing-CY-2018Q2/7aeu-b3qs",
        "data/pbj_data_cms_gov.png",
        delay = 1.0)
```

![](presentation_2_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

### Filter to facility (Beechtree) by provnum


```r
# load and prepare PBJ data from CMS
# https://data.cms.gov/browse?q=pbj&sortBy=relevance
# newyork_2018.rds is a subset of the PBJ data. RDS is a fileformat used in R, which saves column data types.

df_newyork <- read_rds("data/newyork_2018.rds")

# How many long term care facililities in New York state?
df_newyork %>%
  distinct(provname) %>%
  count
```

```
## # A tibble: 1 x 1
##       n
##   <int>
## 1   817
```

```r
# filter to only Beechtree
df <- df_newyork %>%
  filter(provnum == '335017')
```



```r
# explore the data

glimpse(df)
```

```
## Observations: 181
## Variables: 29
## $ provnum             <chr> "335017", "335017", "335017", "335017", "3...
## $ provname            <chr> "BEECHTREE CENTER FOR REHABILITATION AND N...
## $ workdate            <date> 2018-01-01, 2018-01-02, 2018-01-03, 2018-...
## $ mdscensus           <int> 101, 101, 102, 102, 104, 104, 104, 107, 10...
## $ hrs_rndon           <dbl> 0, 8, 8, 8, 8, 0, 0, 8, 8, 8, 8, 8, 0, 0, ...
## $ hrs_rndon_emp       <dbl> 0, 8, 8, 8, 8, 0, 0, 8, 8, 8, 8, 8, 0, 0, ...
## $ hrs_rndon_ctr       <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ hrs_rnadmin         <dbl> 0.00, 24.00, 23.50, 23.50, 25.50, 0.00, 0....
## $ hrs_rnadmin_emp     <dbl> 0.00, 24.00, 23.50, 23.50, 25.50, 0.00, 0....
## $ hrs_rnadmin_ctr     <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ hrs_rn              <dbl> 80.00, 71.00, 75.00, 74.00, 62.50, 46.75, ...
## $ hrs_rn_emp          <dbl> 80.00, 71.00, 75.00, 74.00, 62.50, 46.75, ...
## $ hrs_rn_ctr          <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ hrs_lpnadmin        <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ hrs_lpnadmin_emp    <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ hrs_lpnadmin_ctr    <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ hrs_lpn             <dbl> 62.25, 65.50, 74.25, 74.00, 81.25, 45.75, ...
## $ hrs_lpn_emp         <dbl> 46.25, 49.25, 43.00, 52.75, 42.00, 29.75, ...
## $ hrs_lpn_ctr         <dbl> 16.00, 16.25, 31.25, 21.25, 39.25, 16.00, ...
## $ hrs_cna             <dbl> 169.00, 174.75, 204.50, 197.75, 175.00, 19...
## $ hrs_cna_emp         <dbl> 169.00, 174.75, 197.00, 197.75, 167.50, 19...
## $ hrs_cna_ctr         <dbl> 0.00, 0.00, 7.50, 0.00, 7.50, 2.00, 15.50,...
## $ cna_hprd            <dbl> 1.67, 1.73, 2.00, 1.94, 1.68, 1.86, 1.50, ...
## $ lpn_hprd            <dbl> 0.62, 0.65, 0.73, 0.73, 0.78, 0.44, 0.57, ...
## $ rn_hprd             <dbl> 0.79, 0.70, 0.74, 0.73, 0.60, 0.45, 0.30, ...
## $ day                 <ord> Mon, Tue, Wed, Thu, Fri, Sat, Sun, Mon, Tu...
## $ residents_per_cna   <dbl> 14.4, 13.9, 12.0, 12.4, 14.3, 12.9, 16.0, ...
## $ residents_per_rnlpn <dbl> 17.0, 17.8, 16.3, 16.4, 17.4, 27.0, 27.6, ...
## $ daytype             <ord> weekday, weekday, weekday, weekday, weekda...
```


## Residents per CNA formula

"My measure is 24/HPRD. So 2.4 C.N.A. HPRD = 1 C.N.A. to 10 residents. It probably understates the RN hours during the 1st and 2nd shifts and overstates them on the night shift, but there's nothing to be done about that since the data isn't broken down by shifts." [Jordan Rau, Kaiser Health News journalist]



```r
# Example, April 3, 2018

df %>%
  filter(workdate == "2018-04-03") %>%
  glimpse()
```

```
## Observations: 1
## Variables: 29
## $ provnum             <chr> "335017"
## $ provname            <chr> "BEECHTREE CENTER FOR REHABILITATION AND N...
## $ workdate            <date> 2018-04-03
## $ mdscensus           <int> 88
## $ hrs_rndon           <dbl> 7.5
## $ hrs_rndon_emp       <dbl> 7.5
## $ hrs_rndon_ctr       <dbl> 0
## $ hrs_rnadmin         <dbl> 20.75
## $ hrs_rnadmin_emp     <dbl> 20.75
## $ hrs_rnadmin_ctr     <dbl> 0
## $ hrs_rn              <dbl> 62.32
## $ hrs_rn_emp          <dbl> 62.32
## $ hrs_rn_ctr          <dbl> 0
## $ hrs_lpnadmin        <dbl> 0
## $ hrs_lpnadmin_emp    <dbl> 0
## $ hrs_lpnadmin_ctr    <int> 0
## $ hrs_lpn             <dbl> 59.19
## $ hrs_lpn_emp         <dbl> 26.19
## $ hrs_lpn_ctr         <dbl> 33
## $ hrs_cna             <dbl> 218.35
## $ hrs_cna_emp         <dbl> 210.85
## $ hrs_cna_ctr         <dbl> 7.5
## $ cna_hprd            <dbl> 2.48
## $ lpn_hprd            <dbl> 0.67
## $ rn_hprd             <dbl> 0.71
## $ day                 <ord> Tue
## $ residents_per_cna   <dbl> 9.7
## $ residents_per_rnlpn <dbl> 17.4
## $ daytype             <ord> weekday
```

```r
df %>%
  filter(workdate == "2018-04-03") %>%
  select(mdscensus, hrs_cna)
```

```
## # A tibble: 1 x 2
##   mdscensus hrs_cna
##       <int>   <dbl>
## 1        88    218.
```

```r
df %>%
  filter(workdate == "2018-04-03") %>%
  mutate(cna_hprd = hrs_cna / mdscensus,
         residents_per_cna = 24 / cna_hprd) %>%
  select(workdate, day, mdscensus, hrs_cna, cna_hprd, residents_per_cna)
```

```
## # A tibble: 1 x 6
##   workdate   day   mdscensus hrs_cna cna_hprd residents_per_cna
##   <date>     <ord>     <int>   <dbl>    <dbl>             <dbl>
## 1 2018-04-03 Tue          88    218.     2.48              9.67
```

## Data-ink ratio (Tufte)



## Residents per CNA, 2018 Q2, using ggplot2


```r
# filter to 2018 q2 only
df %>%
  filter(workdate >"2018-03-31",
         workdate < "2018-07-01") %>%
  ggplot(aes(workdate, residents_per_cna), vjust = 0.3) +
  geom_point(size = 2.5, aes(fill = day), shape = 21) + scale_fill_brewer("day") +
  geom_line(size = 0.02) +
  scale_x_date(date_breaks="1 week", date_labels = "%b %d" ) +
  scale_y_continuous(limits = c(8, 15), breaks = seq(8,15, by = 1)) +
  labs(x = "",
       y = "Residents per CNA",
       title = "Residents per CNA, April - June 2018",
       subtitle = "Beechtree Center for Rehabilitation and Nursing, Ithaca NY",
       caption = paste("Residents per CNA = 24 / CNA hours per resident day \nData source: https://data.cms.gov/browse?q=PBJ \nCompiled by Adam Chandler, Beechtree Family Council,", now(), "\n\n")) +
  theme(axis.text.x = element_text(angle = 0, hjust = 0.5),
        axis.ticks.length=unit(0.1, "cm")) +
  theme(panel.grid.minor.y = element_blank(),
        panel.grid.major.y = element_blank(),
        panel.grid.major.x = element_blank(),
        panel.grid.minor.x = element_blank(),
        plot.title = element_text(size=14, face="bold.italic"),
        plot.caption = element_text(size = 7),
        legend.position = c(0.83, 0.97),
        legend.key = element_blank(),
        legend.background = element_blank(),
        legend.direction = "horizontal",
        legend.title = element_blank()
  ) +
  guides(fill=guide_legend(nrow=1)) +
  annotate("text",
           x = as.Date("2018-04-03", "%Y-%m-%d"),
           y = 9.55,
           label = "Tue Apr 03",
           size = 1.5)
```

![](presentation_2_files/figure-html/unnamed-chunk-9-1.png)<!-- -->



```r
ggsave(plot = last_plot(),
       filename = "output/beechtree_cna_staffing_pbj_2018_apr-jun.png",
       width=11,
       height=7,
       dpi = 600)

ggsave(plot = last_plot(),
       filename = "output/beechtree_cna_staffing_pbj_2018_apr-jun.pdf",
       width=11,
       height=8.5,
       dpi = 600)
```



## Beechtree Family Council

#### Website: https://osf.io/awfhp/wiki/home/ 

#### References

Butler, Matt. “Nursing Home Shade.” Ithaca Times, November 18, 2018, pp. 1,7-8. https://issuu.com/ithacatimes/docs/november_14__2018

“Staffing Issues at New York Nursing Homes.” News Radio 810 & 103.1 WGY, July 27, 2018. https://wgy.iheart.com/content/2018-07-27-staffing-issues-at-new-york-nursing-homes/.

Rau, Jordan. “New Data Set Helps Reporter Pinpoint Critical Staffing Concerns in Skilled Nursing Facilities.” Association of Health Care Journalists, July 25, 2018. https://healthjournalism.org/resources-articles-details.php?id=429#.W1nMUNIzq00.

“‘Like A Ghost Town’: Erratic Nursing Home Staffing Revealed Through New Records,” July 18, 2018. Huffington Post. https://www.huffingtonpost.com/entry/like-a-ghost-town-erratic-nursing-home-staffing_us_5b4e511ae4b013392edfa262.

Teller, Sara E. “Medicare Ratings Updated with Nursing Home Staffing Rates.” Legal Reader (blog), July 16, 2018. https://www.legalreader.com/medicare-ratings-updated-nursing-home-staffing-rates/.

Rau, Jordan, and Kaiser Health News. “‘Like a Ghost Town’: Erratic Nursing Home Staffing Revealed through New Records - Philly.” Philly.com, July 13, 2018. http://www.philly.com/philly/health/like-a-ghost-town-erratic-nursing-home-staffing-revealed-through-new-records-20180713.html.

“Most Nursing Homes Are Not Adequately Staffed, New Federal Data Says.” PBS NewsHour, July 13, 2018. https://www.pbs.org/newshour/health/most-nursing-homes-are-not-adequately-staffed-new-federal-data-says.

Rau, Jordan. “‘Like A Ghost Town’: Erratic Nursing Home Staffing Revealed Through New Records.” Kaiser Health News (blog), July 13, 2018. https://khn.org/news/like-a-ghost-town-erratic-nursing-home-staffing-revealed-through-new-records/.

Rau, Jordan. “‘It’s Almost Like a Ghost Town.’ Most Nursing Homes Overstated Staffing for Years.” The New York Times, July 8, 2018, sec. Health. https://www.nytimes.com/2018/07/07/health/nursing-homes-staffing-medicare.html.

Brown, Jessica. “Letter: Thanks for Repairing Mechanized Wheelchairs.” Ithaca Journal, June 23, 2018. https://www.ithacajournal.com.



```r
shot_nyt <- "data/Rau_Jordan_Ghost_Town_NYTimes_July_8_2018.png"

webshot("https://khn.org/news/like-a-ghost-town-erratic-nursing-home-staffing-revealed-through-new-records/",
        shot_nyt,
        delay = 1.0)
```

![](presentation_2_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

