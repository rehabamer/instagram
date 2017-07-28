# instagram
install.packages('instaR')
library(instaR)
require(httr)
require(rjson)
require(RCurl)
install.packages("devtools")
install.packages("Rcpp")
library(devtools)
library(Rcpp)

#Authentication

options(RCurlOptions = list(verbose = FALSE, capath = system.file("CurlSSL", "cacert.pem", package = "RCurl"), ssl.verifypeer = FALSE))

## getting callback URL
full_url <- oauth_callback()
full_url <- gsub("(.*localhost:[0-9]{1,5}/).*", x=full_url, replacement="\\1")
message <- paste("Copy and paste into Site URL on Instagram App Settings:", 
                 full_url, "\nWhen done, press any key to continue...")

invisible(readline(message))


app_name <- "xxxxx"
client_id <- "xxxxxx"
client_secret <- "xxxxx"
scope = "public_content"

instagram <- oauth_endpoint(
  authorize = "https://api.instagram.com/oauth/authorize",
  access = "https://api.instagram.com/oauth/access_token")  
myapp <- oauth_app(app_name, client_id, client_secret)


instaOAuth(client_id, client_secret, scope = c("public_content"))
save(ig_oauth, file = "my_oauth")

#download most recent media items from Instagram
media <- fromJSON(getURL(paste('https://api.instagram.com/v1/users/self/media/recent/?access_token=',token,sep="")))

#extract the count of likes and comments and the date and time the photo was posted.
df = data.frame(no = 1:length(media$data))

for(i in 1:length(media$data))
{
  #comments
  df$comments[i] <-media$data[[i]]$comments$count
  
  #likes:
  df$likes[i] <- media$data[[i]]$likes$count
  
  #date
  df$date[i] <- toString(as.POSIXct(as.numeric(media$data[[i]]$created_time), origin="1970-01-01"))
}

