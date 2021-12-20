# Metadata
Organize and store documentation and metadata in this folder.

Metadata files should be listed for relevant data sources in [data/data_metadata.csv](../data_metadata.csv)

# Twitter Data in Ida.Rdata

This data was acquried with the `rtweet` package and `search tweets` Twitter API with two searches.

The `tevent` data frame contains tweets for hurricane Ida, searched three times (on September 2, 5, and 10, 2021) with the following code:
```r
tevent_raw <- search_tweets("hurricane OR ida", n=200000, include_rts=FALSE, token=twitter_token, geocode="36,-87,1000mi", retryonratelimit=TRUE)
```

as well as this search on September 10, 2021 for additional thematic coverage:
```r
tevent_raw4 <- search_tweets("ida OR flood OR electricity OR recovery OR outage", n = 200000, include_rts = TRUE, token = twitter_token, geocode = "36,-87,1000mi", retryonratelimit = TRUE)
```

The `tdcontrol` data frame contains tweets without any text filter searched on November 19, 2019 with the following code:
```r
tdcontrol_raw <- search_tweets("-filter:verified OR filter:verified", n=200000, include_rts=FALSE, token=twitter_token, geocode="32,-78,1000mi", retryonratelimit=TRUE)
```

Note that the code requries a valid `twitter_token` object in order to run correctly, and the `search_tweets` function cannot conduct a historical search. If you need to reproduce these results, you will need historic access to archived twitter data, and some tweets may have been edited or removed since the search was conducted.

Following the search, the data was also filtered for more precise geographic locations and converted into point features with the following code.

```r
#convert GPS coordinates into lat and lng columns
tevent <- lat_lng(tevent_raw,coords=c("coords_coords"))
tdcontrol <- lat_lng(tdcontrol_raw,coords=c("coords_coords"))

#select any tweets with lat and lng columns (from GPS) or designated place types of your choosing
tevent <- subset(tevent, place_type == 'city'| place_type == 'neighborhood'| place_type == 'poi' | !is.na(lat))
tdcontrol <- subset(tdcontrol, place_type == 'city'| place_type == 'neighborhood'| place_type == 'poi' | !is.na(lat))

#convert bounding boxes into centroids for lat and lng columns
tevent <- lat_lng(tevent,coords=c("bbox_coords"))
tdcontrol <- lat_lng(tdcontrol,coords=c("bbox_coords"))
```
