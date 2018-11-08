# random-marine-species
Get a random, valid marine species and display its WoRMS page

Very preliminary function to select a marine species at random from all those occurring in WoRMS, and to open its WoRMS page. First load required pacakges:
```R
library(worrms)
library(tidyverse)
```
Here's the function:
```R
get_rand_sp <- function(){

  df <- wm_children(1)
  counter <- 1
  while(!"Species" %in% df$rank){
  	counter <- counter + 1
  	df1 <- try(wm_children(sample(df$valid_AphiaID, 1)), silent = TRUE)
  	if(!("try-error" %in% class(df1))){
  	  df <- df1
  	} else if (nrow(df) == 1) {
  	      df <- wm_children(1)
  	} else if (counter > 1000) {
      break
  	}
    }
    df <- df %>% filter(rank == "Species") %>% sample_n(1)
    df_rec <- wm_record(df$valid_AphiaID)
    df_rec
}
```
Run it like this:
```R
rand_sp <- get_rand_sp()
browseURL(url = rand_sp$url)
```
