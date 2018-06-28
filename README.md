# Victorian-Train-Map
For one of my Data Visualisation class assignemnts i set out to make a interactive map of the Victorian Train Map. I used the Leaflet Packge (https://rstudio.github.io/leaflet/). Leaflet was used over GoogleVis due to Leaflet ability to hold more data points, and create a more interactive map. 

### [Project Inspiration](https://www.reddit.com/r/dataisbeautiful/comments/6baefh/berlin_subway_map_compared_to_its_real_geography/)
  
### [Melbourne Train Map](https://i.imgur.com/5BMDHQE.jpg)
    
### [Date source](https://www.data.vic.gov.au/data/dataset/ptv-timetable-and-geographic-information-2015-gtfs)
The Data source has all the public trainsport, stops, lines, routes and times for trians, trams and buses in Victoria. 

### ![Image of my Map](https://raw.githubusercontent.com/Npennell96/Victorian-Train-Map/master/Train%20Map%20Image.PNG?token=Ako9J0DsPaZXn85q6rcXGNHG88a8cou_ks5bPWm9wA%3D%3D)

### [Rpubs Link for Interactive Map](https://rpubs.com/Npennell/Vic-train-map)


## Used Packages
```
library(dplyr)
library(devtools)
library(stringr)
library(tidyr)
library(leaflet)
library(htmlwidgets)
```

## Fromating Data
I remaned each file to represents what the subsection the data reflects, E.g; Bus (Metro Trains), Bus 2, Bus 3, Bus 4, Country Bus (Diffrent to Regional Buses), Metro Trains, Regoinal Bus, Sky Bus (Bus to the Airport), Tram, vline(regional Trains). 

```
# Loading Data
  # Metro Trains
    # Metro Station
      Train.Station <- read.table("C:/RMIT/Data vis/data/gtfs/metro trains/google_transit/stops.txt", 
                                  header = TRUE, sep = ",")
      colnames(Train.Station) <- c("Stop.ID", "Station", "Latitude", "Longitude")
      Train.Station <- separate(Train.Station, Station, c("Station", "Line"), sep = "[()]")
    # Metro Routes
      Metro.Routes <- read.table("C:/RMIT/Data vis/data/gtfs/metro trains/google_transit/shapes.txt", 
                                 header = TRUE, sep = ",")
      colnames(Metro.Routes) <- c("Shape.ID", "Latitude", "Longitude", "Sequence", "dist_traveled")
      Metro.Routes <- separate(Metro.Routes, Shape.ID, c("Direcion", "line", 
                                                         "T/F", "mjp", "ID"), sep = "-")
    Metro.lines <- Metro.Routes[!duplicated(Metro.Routes$line), ]
  # V/line Trains
    # V/line Station
      Vline <- read.table("C:/RMIT/Data vis/data/gtfs/vline/google_transit/stops.txt", 
                          header = TRUE, sep = ",")
      colnames(Vline) <- c("Stop.ID", "Station", "Latitude", "Longitude")
      Vline <- separate(Vline, Station, c("Station", "Line"), sep = "[()]")
    # V/line Routes
      Vline.Routes <- read.table("C:/RMIT/Data vis/data/gtfs/vline/google_transit/shapes.txt", 
                                 header = TRUE, sep = ",")
      colnames(Vline.Routes) <- c("Shape.ID", "Latitude", "Longitude", "Sequence", "dist_traveled")
      Vline.Routes <- separate(Vline.Routes, Shape.ID, c("Direcion", "line", 
                                                         "T/F", "mjp", "ID"), sep = "-")
      Vline.lines <- Vline.Routes[!duplicated(Vline.Routes$line), ]
```
For the Routes I Split the data into each line. Then removed the extra routes, based on trial and error, as not all routes go the full journey and there was no clear pattion for which do.

```
# Formatting Train Routes     
  # Metro Routes by Line
    # Alamein
      ALM <- Metro.Routes[Metro.Routes$line == "ALM", ]
      ALM <- ALM[ALM$ID == "1.2.H", ]
      ALM <- ALM[ALM$`T/F` == "F", ]
    # Craigburn
      B31 <- Metro.Routes[Metro.Routes$line == "B31", ]
      B31 <- B31[B31$ID == "1.2.H", ]
      B31 <- B31[B31$`T/F` == "F", ]
    # Belgrave
      BEL <- Metro.Routes[Metro.Routes$line == "BEL", ]
      BEL <- BEL[BEL$ID == "1.2.H", ]
      BEL <- BEL[BEL$`T/F` == "F", ]
    # Cranbourn
      CRB <- Metro.Routes[Metro.Routes$line == "CRB", ]
      CRB <- CRB[CRB$ID == "1.2.H", ]
      CRB <- CRB[CRB$`T/F` == "B", ]
    # Epping 
      EPP <- Metro.Routes[Metro.Routes$line == "EPP", ]
      EPP <- EPP[EPP$ID == "1.1.H", ]
      EPP <- EPP[EPP$`T/F` == "B", ]
    # Frankston
      FKN <- Metro.Routes[Metro.Routes$line == "FKN", ]
      FKN <- FKN[FKN$ID == "1.2.H", ]
      FKN <- FKN[FKN$`T/F` == "F", ]
    # Glen Waverley
      GLW <- Metro.Routes[Metro.Routes$line == "GLW", ]
      GLW <- GLW[GLW$ID == "1.2.H", ]
      GLW <- GLW[GLW$`T/F` == "F", ]
    # Hurstbridge
      HBG <- Metro.Routes[Metro.Routes$line == "HBG", ]
      HBG <- HBG[HBG$ID == "1.2.H", ]
      HBG.1 <- HBG[HBG$`T/F` == "F", ]
      HBG.2 <- HBG[HBG$`T/F` == "E", ]
    # Lilydale
      LIL <- Metro.Routes[Metro.Routes$line == "LIL", ]
      LIL <- LIL[LIL$ID == "1.2.H", ]
      LIL <- LIL[LIL$`T/F` == "F", ]
    # Pakeham
      PKM <- Metro.Routes[Metro.Routes$line == "PKM", ]
      PKM <- PKM[PKM$ID == "1.2.H", ]
      PKM <- PKM[PKM$`T/F` == "F", ]
    # Sandringham
      SDM <- Metro.Routes[Metro.Routes$line == "SDM", ]
      SDM <- SDM[SDM$ID == "1.2.H", ]
      SDM <- SDM[SDM$`T/F` == "F", ]
    # Stony Point
      SPT <- Metro.Routes[Metro.Routes$line == "SPT", ]
      SPT <- SPT[SPT$ID == "1.2.H", ]
    # Sunbury
      SYM <- Metro.Routes[Metro.Routes$line == "SYM", ]
      SYM <- SYM[SYM$ID == "1.2.H", ]
      SYM <- SYM[SYM$`T/F` == "F", ]
    # Upfield
      UFD <- Metro.Routes[Metro.Routes$line == "UFD", ]
      UFD <- UFD[UFD$ID == "1.2.H", ]
      UFD <- UFD[UFD$`T/F` == "F", ]
    # Werribee 
      WBE <- Metro.Routes[Metro.Routes$line == "WBE", ]
      WBE.1 <- WBE[WBE$ID == "1.2.H", ]
      WBE.1 <- WBE.1[WBE.1$`T/F` == "B", ]
      WBE.2 <- WBE[WBE$ID == "1.5.H", ]
      WBE.2 <- WBE.2[WBE.2$`T/F` == "A", ]
    # Williamstown 
      WMN <- Metro.Routes[Metro.Routes$line == "WMN", ]
      WMN <- WMN[WMN$ID == "1.2.H", ]
      WMN <- WMN[WMN$`T/F` == "F", ]
    
  # V/line Routes by line
    # Echuca
      Ech <- Vline.Routes[Vline.Routes$line == "Ech", ]
      Ech <- Ech[Ech$ID == "1.1.H", ]
      Ech <- Ech[Ech$`T/F` == "F", ]
    # Maryborough
      my1 <- Vline.Routes[Vline.Routes$line == "my1", ]
      my1 <- my1[my1$ID == "1.1.H", ]
      my1 <- my1[my1$`T/F` == "F", ]
    # Shepparton
      sht <- Vline.Routes[Vline.Routes$line == "Sht", ]
      sht <- sht[sht$ID == "1.1.H", ]
      sht <- sht[sht$`T/F` == "F", ]
    # Alubury
      V01 <- Vline.Routes[Vline.Routes$line == "V01", ]
      V01 <- V01[V01$ID == "1.1.H", ]
      V01 <- V01[V01$`T/F` == "F", ]
    # Ararat
      V04 <- Vline.Routes[Vline.Routes$line == "V04", ]
      V04 <- V04[V04$ID == "1.1.H", ]
      V04 <- V04[V04$`T/F` == "F", ]
    # Bacchus Marsh
      V05 <- Vline.Routes[Vline.Routes$line == "V05", ]
      V05 <- V05[V05$ID == "1.1.H", ]
      V05 <- V05[V05$`T/F` == "F", ]
    # Bairnsdale
      V08 <- Vline.Routes[Vline.Routes$line == "V08", ]
      V08 <- V08[V08$ID == "1.1.H", ]
      V08 <- V08[V08$`T/F` == "A", ]
    # Bendigo
      V12 <- Vline.Routes[Vline.Routes$line == "V12", ]
      V12 <- V12[V12$ID == "1.1.H", ]
      V12 <- V12[V12$`T/F` == "A", ]
    # Waurn Ponds
      V23 <- Vline.Routes[Vline.Routes$line == "V23", ]
      V23 <- V23[V23$ID == "1.1.H", ]
      V23 <- V23[V23$`T/F` == "A", ]
    # Seymour
      V40 <- Vline.Routes[Vline.Routes$line == "V40", ]
      V40 <- V40[V40$ID == "1.1.H", ]
      V40 <- V40[V40$`T/F` == "A", ]
    # Swan Hill
      V45 <- Vline.Routes[Vline.Routes$line == "V45", ]
      V45 <- V45[V45$ID == "1.1.H", ]
      V45 <- V45[V45$`T/F` == "A", ]
    # Traralogn
      V48 <- Vline.Routes[Vline.Routes$line == "V48", ]
      V48 <- V48[V48$ID == "1.1.H", ]
      V48 <- V48[V48$`T/F` == "A", ]
    # Warrnambool
      V51 <- Vline.Routes[Vline.Routes$line == "V51", ]
      V51 <- V51[V51$ID == "1.1.H", ]
      V51 <- V51[V51$`T/F` == "A", ]
    # Nhill not on metro map
      Vov <- Vline.Routes[Vline.Routes$line == "Vov", ]
      Vov <- Vov[Vov$ID == "1.1.H", ]
      Vov <- Vov[Vov$`T/F` == "E", ]
    # Dandenong
      vPK <- Vline.Routes[Vline.Routes$line == "vPK", ]
      vPK <- vPK[vPK$ID == "1.1.H", ]
      vPK <- vPK[vPK$`T/F` == "A", ]
    # Sunbury
      vsu <- Vline.Routes[Vline.Routes$line == "vsu", ]
      vsu <- vsu[vsu$ID == "1.1.H", ]
      vsu <- vsu[vsu$`T/F` == "A", ]
```

## Visualisation Code
```
# Mapping with Leaflet
  # Icons
    Metro.Train.Icon <- awesomeIcons(icon = 'android-subway', iconColor ='#0093ce' , library = 'ion',
                                     markerColor = 'white' ) 
    Vline.Icon <- awesomeIcons(icon = 'android-subway', iconColor ='#853d91' , library = 'ion',
                               markerColor = 'white' )
  
    # Titles (map layers)
    r <- leaflet(options = leafletOptions(minZoom = 7)) %>% addTiles(group = "OSM (default)") %>% 
      addProviderTiles(providers$Stamen.TonerLite, group = "Toner Lite (Simple)") %>%
    
     # V/line Routes
      addPolylines(data = V45, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V45, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,5') %>%
      addPolylines(data = V51, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V51, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,55') %>%
      addPolylines(data = Ech, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = Ech, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,5') %>%
      addPolylines(data = my1, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = my1, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,5') %>%
      addPolylines(data = sht, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = sht, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,5') %>%
      addPolylines(data = V01, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V01, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,5') %>%
      addPolylines(data = V04, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V04, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,5') %>%
      addPolylines(data = V08, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V08, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff', group = "V/line Routes", weight = 2, dashArray = '5,5') %>%
      addPolylines(data = V05, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V12, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V23, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V40, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = V48, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = vPK, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      addPolylines(data = vsu, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
      # note vov not on metro map
      addPolylines(data = Vov, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#853d91', group = "V/line Routes") %>%
    # Metro Routes
      addPolylines(data = ALM, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#005592', group = "Metro Routes") %>%
      addPolylines(data = B31, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ebbf14', group = "Metro Routes") %>%
      addPolylines(data = BEL, lat = ~Latitude, lng = ~Longitude,opacity = 1, 
                   color = '#005592', group = "Metro Routes") %>%
      addPolylines(data = CRB, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#02a0c7', group = "Metro Routes") %>%
      addPolylines(data = EPP, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ce1d2f', group = "Metro Routes") %>%
      addPolylines(data = FKN, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#07903e', group = "Metro Routes") %>%
      addPolylines(data = GLW, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#005592', group = "Metro Routes") %>%
      addPolylines(data = HBG.1, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ce1d2f', group = "Metro Routes") %>%
      addPolylines(data = HBG.2, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ce1d2f', group = "Metro Routes") %>%
      addPolylines(data = LIL, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#005592', group = "Metro Routes") %>%
      addPolylines(data = PKM, lat = ~Latitude, lng = ~Longitude,opacity = 1,
                   color = '#02a0c7', group = "Metro Routes") %>% 
      addPolylines(data = SDM, lat = ~Latitude, lng = ~Longitude, opacity = 1,
                   color = '#f27db1', group = "Metro Routes") %>%
      addPolylines(data = SPT, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#07903e', group = "Metro Routes") %>%
      addPolylines(data = SPT, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ffffff',weight = '2', group = "Metro Routes") %>%
      addPolylines(data = SYM, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ebbf14', group = "Metro Routes") %>%
      addPolylines(data = UFD, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#ebbf14', group = "Metro Routes") %>%
      addPolylines(data = WBE.1, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#07903e', group = "Metro Routes") %>%
      addPolylines(data = WBE.2, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#07903e', group = "Metro Routes") %>%
      addPolylines(data = WMN, lat = ~Latitude, lng = ~Longitude, opacity = 1, 
                   color = '#07903e', group = "Metro Routes") %>%
    
    # Adding markers
      addAwesomeMarkers(data = Vline, lng = ~Longitude, lat = ~Latitude, group = "V/line Station's", 
                        label = ~Station, icon = Vline.Icon) %>%
      addAwesomeMarkers(data = Train.Station, lng = ~Longitude, lat = ~Latitude, 
                        label = ~Station, icon = Metro.Train.Icon, group = "Metro Station's") %>%
    # Legend
      addLegend(position = 'bottomleft', colors = c('#ce1d2f',
                                                    '#ebbf14',
                                                    '#005592',
                                                    '#02a0c7',
                                                    '#07903e',
                                                    '#f27db1',
                                                    '#ffffff',
                                                    '#853d91'),opacity = 1, 
                labels = c("South Morang & Hustbridge Line", 
                           "Sunbury, Craigieburn & Upfield Line",
                           "Lilydale, Belgrave, Alamein & Glen waverly Line",
                           "Pakenham & Cranbourne Line",
                           "Frankston, Werribee & Williamstown Line",
                           "Sandringhman Line",
                           "Stony Point Line",
                           "V/line (dashes requier paper ticket)"), title = 'Line Legend', 
                group = "Line Legend") %>%
      
      
    # Layer Control
      addLayersControl(overlayGroups = c("Metro Station's", "V/line Station's", "Metro Routes",
                                         "V/line Routes", "Line Legend"),  
                       baseGroups = c("OSM (Default)", "Toner Lite (Simple)"),
                       options = layersControlOptions(collapsed = TRUE, autoZIndex = TRUE) )
```

## Caption
The map has the option to turn on and off, markers, the over lay routes and the titles. This allows someone viewing the select the parts they would like to focus on.
  
There are 3 title options the first being the default Leaflet one, the second being  Toner Lite, a more simple of a map, the third being no title for if you wanted to just look at the shape of the lines.  
  
Each station is marked on the map with a marker, V/line stations in white with a purple icon, metro stations white with blue icons. While each train route was added as a polyline, with the colour matched to the colour it is given on the metro map, through the use of MS paint.  
  
**Notes**:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- The line that goes out to Nhill through Horsham isn't on the metro map but metro has data on it till it reaches Nhill. I think because it's the line to Adelaide.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-	White lines and white dashes added by adding a polyline on the same route just with a less weighted line.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-	Some of the V/line dashes start to early.
