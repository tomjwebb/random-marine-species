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
Problem with this is it selects a group at each level at random, but this is not weighted by the number of species in this group - e.g. at level 1, Animalia and Fungi have the same change of being accepted, despite very different total numbers of species in WoRMS. Alternative brute force approach is to select an Aphia ID as a random integer from within the range of known Aphia IDs, as in this function:
```R
get_rand_sp2 <- function(){
  # generate feasible aphia ID to try
  gottit <- FALSE
  while(gottit == FALSE){
    df <- numeric()
    class(df) <- "try-error"
    while("try-error" %in% class(df)){
      id <- as.integer(runif(1, 10000, 1300000))
      df <- try(wm_record(id), silent = TRUE)
      }
    if(identical(df$AphiaID, df$valid_AphiaID) & identical(df$rank, "Species")){
  	  gottit <- TRUE
    }
  }
  df
}
```
Run it like this:
```R
rand_sp <- get_rand_sp2()
browseURL(url = rand_sp$url)
```
