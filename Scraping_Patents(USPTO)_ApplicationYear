#M1:Import patents data##############################################################################################
library(tidyr)
library(XML)

#data import
rawdata <- read.csv(paste("./Input/Rawdata_total_CSV.csv",sep=""),stringsAsFactors = FALSE)

#US 특허만 필터링
rawdata.us <- rawdata[which(grepl("US", rawdata$번호) == TRUE), ]

# us referece data
b <- strsplit(as.character(rawdata.us$자국인용특허), split = ",", fixed = FALSE)
indx <- lengths(b)
rawdata.ref.spread <- as.data.frame(do.call(rbind, lapply(b, 'length<-', max(indx))))
rawdata.ref.spread <- mutate(ID = rownames(rawdata.ref.spread), rawdata.ref.spread)
rawdata.ref.spread <- cbind(rawdata.ref.spread[ncol(rawdata.ref.spread)], rawdata.ref.spread[1:ncol(rawdata.ref.spread)-1])
rawdata.ref.spread <- data.frame(lapply(rawdata.ref.spread, as.character), stringsAsFactors=FALSE)
rawdata.ref.spread <- as.data.frame(rawdata.ref.spread)

#DF 만들기
DF <- cbind(rawdata.us, rawdata.ref.spread)
#write.csv(DF, file ="./Output/DF_CPCspread_CSV.csv",row.names=FALSE)

#가로 배열된 CPC를 세로 배열
#CPC분율 가산
library(stringr)
library(reshape)
ymd <- DF$번호
cpc.value <- DF$일련번호
#특허 DB 쪼개고 붙이기
DF.DF <- cbind(ymd, cpc.value, DF[,63:ncol(DF)])
#가로 배열된 CPC를 세로 배열
#options(java.parameters = "-Xmx16000m")
DF.cpcspread <- melt(DF.DF, id.vars=1:2)
#write.csv(DF.cpcspread, file ="./DF_cpcspread.csv",row.names=FALSE)
#DF.cpcspread <- read.csv(file="./DF_cpcspread.csv", head=TRUE)
#rm(ap.pr.ymd, ap.ymd, DF.DF)

colnames(DF.cpcspread) <- c("original.number", "id", "variable", "references.number")
DF.cpcspread <- DF.cpcspread[!is.na(DF.cpcspread$references.number), ]

#결과저장
write.csv(DF.cpcspread, file ="./Output/Output_table(id_num_references)_CSV.csv",row.names=FALSE)










#M2:USTPO Patents application year Crawler#########################################################################
library(RCurl)
library(XML)
library(stringi)
library(dplyr)
library(tidyr)
library(stringr)
library(lubridate)
library(qdapRegex)

#크롤링 US number 추출
#rn <- read.table("./Input/PN.txt",sep=",")
rn <- data.frame(id = DF.cpcspread$id, original.number = DF.cpcspread$original.number, references.number = DF.cpcspread$references.number)
rn <- rn[order(rn$id),]
rn$references.number.crawl <- gsub("US", "", rn$references.number)

#괄호 안의 등록 번호 추출
#rn$references.number.crawl.round <- rm_round(rn$references.number.crawl, extract=TRUE)
#rn$references.number.crawl[which(!is.na(rn$references.number.crawl.2))] <- rn$references.number.crawl.2[which(!is.na(rn$references.number.crawl.2))]

#출원번호는 year로 이동
rn$references.number.crawl <- as.character(rn$references.number.crawl)
rn.1 <- separate(rn, 'references.number.crawl', into = c("references.number.crawl.2","trash"), sep = "/")
rn.1 <- rn.1[c("id", "original.number", "references.number", "references.number.crawl.2")]

rn.1$year[which(grepl(".\\d{4}", rn.1$references.number.crawl.2) == FALSE)] <- rn.1$references.number.crawl.2[which(grepl(".\\d{4}", rn.1$references.number.crawl.2) == FALSE)]
rn.1$references.number.crawl.2[which(grepl(".\\d{4}", rn.1$references.number.crawl.2) == FALSE)] <- ""

#make rn
rn <- rn.1
rownames(rn) <- 1:nrow(rn)

temp <- data.frame()

for(i in 1:10)#nrow(rn))
{  
  #i <- 6
  if(rn$references.number.crawl.2[i] != "")
  {
    url<-paste("http://patft.uspto.gov/netacgi/nph-Parser?Sect1=PTO1&Sect2=HITOFF&d=PALL&p=1&u=%2Fnetahtml%2FPTO%2Fsrchnum.htm&r=1&f=G&l=50&s1=",rn$references.number.crawl.2[i],".PN.&OS=PN/",rn$references.number.crawl.2[i],"&RS=PN/", rn$references.number.crawl.2[i], sep="")
    
    # finding first reference number
    xml <- xmlParse(url, isHTML = TRUE)
    root <- xmlRoot(xml)
    xml_child <- xmlChildren(root)
    contents <- xml_child[[2]]
    txt <- xmlValue(contents)
    txt <- gsub("\n", " ", txt)
    
    pattern.start <- c("United States Patent")
    pattern.end <- c("Abstract")
    
    start.ref <- regexpr(pattern.start, txt)
    end.ref <- regexpr(pattern.end, txt)
    
    txt.year <- substr(txt, start=start.ref[1], stop=end.ref[1])
    
    txt.year <- regmatches(txt.year, gregexpr("\\d{4}", txt.year))
    txt.year <- data.frame(appl.year = txt.year)
    colnames(txt.year) <- ("appl.year")
    
    #result <- data.frame(data = rn[i,], appl.year = txt.year)
    result <- cbind(rn[i,], txt.year)
    temp <- rbind(temp, result) 
    print(i)
  }else{
    txt.year <- data.frame(appl.year = "")
    colnames(txt.year) <- ("appl.year")
    result <- cbind(rn[i,], txt.year)
    temp <- rbind(temp, result) 
  }
}
temp <- as.data.frame(temp)
write.csv(temp, file="./Output/Results_total.csv", row.names = FALSE)









#M3:USTPO Patents References Crawler################################################################################
library(RCurl)
library(XML)
library(stringi)
library(dplyr)
library(tidyr)
library(stringr)
library(lubridate)

rn <- read.table("./Input/PN.txt",sep=",")

temp <- data.frame()

for(i in 1:nrow(rn))
{  
  #i <- 3
  url<-paste("http://patft.uspto.gov/netacgi/nph-Parser?Sect1=PTO1&Sect2=HITOFF&d=PALL&p=1&u=%2Fnetahtml%2FPTO%2Fsrchnum.htm&r=1&f=G&l=50&s1=",rn[i,],".PN.&OS=PN/",rn[i,],"&RS=PN/", rn[i,], sep="")

  # finding first reference number
  xml <- xmlParse(url, isHTML = TRUE)
  root <- xmlRoot(xml)
  xml_child <- xmlChildren(root)
  contents <- xml_child[[2]]
  txt <- xmlValue(contents)
  txt <- gsub("\n", " ", txt)
  
  pattern.start <- c("References Cited")
  pattern.end <- c("Claims")

  start.ref <- regexpr(pattern.start, txt)
  end.ref <- regexpr(pattern.end, txt)
  
  txt.references <- substr(txt, start=start.ref[1], stop=end.ref[1])
  txt.references.first <- stri_extract_first_regex(txt.references, "[0-9]+")
  if(txt.references.first != 1) #Referenced Cited 라는 단어가 특허 명세서 상에 있어야 아래 단계 수행
  {
    #finding reference table
    doc<- htmlTreeParse(url, useInternalNodes=TRUE)
    doc.table <- readHTMLTable(doc)
    names(doc.table) <- c(1:length(doc.table))
    for(j in 1:length(doc.table))
    {  
      #j <- 6
      if(length(which(doc.table[[j]] == as.numeric(txt.references.first))) != 0){
        table.num.1 <- j
        table.num.2 <- j+1
      }
    }
    table.num.1 <- as.numeric(table.num.1)
    table.num.2 <- as.numeric(table.num.2)
    
    #uspto.references.table
    uspto.references.table <- doc.table[[table.num.1]]
    colnames(uspto.references.table) <- c("patent.number","myyyy","inventors")
    uspto.references.table <- uspto.references.table[which(is.na(uspto.references.table$myyyy) == FALSE),]
    
    #foreign.references.table
    foreign.references.table <- doc.table[[table.num.2]]
    colnames(foreign.references.table) <- c("null","patent.number","null","myyyy","null","inventors")
    foreign.references.table <- foreign.references.table[c(-1, -3, -5)]
    foreign.references.table <- foreign.references.table[which(is.na(foreign.references.table$myyyy) == FALSE),]
    
    #rbind result
    references.table <- rbind(uspto.references.table, foreign.references.table)
    
    #cbind original number
    references.table <- cbind(data.frame(original.number = rep(rn[1,], nrow(references.table))), references.table)
    
    #split month/year
    references.table$myyyy <- as.character(references.table$myyyy)
    references.table.1 <- separate(references.table, 'myyyy', into = c("m","year"), sep = " ")
    references.table <- references.table.1
    
    #calculate average application year of references
    references.table$year <- year(as.Date(as.character(references.table$year), format = "%Y"))
    references.table$year.mean <- mean(references.table$year)
    
    #Saving results
    write.csv(references.table,file=paste0("./Output/Output_",i,"_",rn[i,],".csv"), row.names = FALSE)
    
    #Saving results
    result <- data.frame(original.number = rn[i,], year.mean = references.table$year.mean[1])
    write.csv(result,file=paste0("./Output/Results_",i,"_",rn[i,],".csv"), row.names = FALSE)
    
    #memory
    temp <- rbind(temp, result)
  }else{
    result <- data.frame(original.number = rn[i,], year.mean = "")
    temp <- rbind(temp, result)
  }
  print(i)
}
write.csv(temp,file=paste0("./Output/Results_total.csv"), row.names = FALSE)
