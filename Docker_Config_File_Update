###################################################################################################
# This code will take update the CPP image with the database new host IP address.
###################################################################################################

# Code overview ###################################################################################
# 1. Get host IP address from mysql container
# 2. Copy .my.cnf from CPP container and paste it in local host
# 3. Change local host .my.cnf file with new host IP address
# 4. Copy back local host .my.cnf file into CPP container
# 5. Commit the CPP container as a new image
###################################################################################################

# Example of how it looks from my terminal#########################################################
# root@mz-desktop:~/ECSU_CSC480_Fall2018# Rscript MySQL_Network_Update.R focused_borg eager_albattani mz/cpp:8.0
# [1] "mysql_name_input: focused_borg"
# [1] "cpp_name_input: eager_albattani"
# [1] "new_cpp_name_input: mz/cpp:8.0"
# [1] "---------------------"
# [1] "focused_borg IP address: 172.17.0.3"
# [1] "old .my.cnf IP address: 172.17.0.2"
# [1] "docker commit eager_albattani mz/cpp:8.0"
# sha256:70ee27674d6e694e7874b4ec722511a2a7ee888645b3e2a1cfe16d39a1ff909b
# [1] "DONE! New image created!"
# root@mz-desktop:~/ECSU_CSC480_Fall2018#
###################################################################################################

#Saved if you rather use RStudio and the console for input
#mysql_name_input <- readline(prompt = "Enter the mysql container name: ")
#cpp_name_input <- readline(prompt = "Enter the current CPP image name: ")
#new_cpp_name_input <- readline(prompt = "Enter the new CPP image name: ")

args <- commandArgs(TRUE)

mysql_name_input <- args[1]
cpp_name_input <- args[2]
new_cpp_name_input <- args[3]

print(paste("mysql_name_input:", mysql_name_input))
print(paste("cpp_name_input:", cpp_name_input))
print(paste("new_cpp_name_input:", new_cpp_name_input))
print("---------------------")

get_ipaddress <- function (mysqlname){
  bridge <- system("docker network inspect bridge", intern = TRUE)
  mysql_name_index<-grep(mysqlname, bridge)
  bridge <-bridge[c(mysql_name_index:length(bridge))]
  mysql_name_index<-grep(mysqlname, bridge)
  mysql_name_index2 <- grep("}", bridge)
  ipaddress <- bridge[c(mysql_name_index:mysql_name_index2[1])]
  ipaddress_index <- grep("IPv4Address", ipaddress)
  ipaddress <- ipaddress[ipaddress_index]
  ipaddress <- strsplit(ipaddress, ':')[[1]]
  ipaddress <- trimws(ipaddress)
  ipaddress <- ipaddress[2]
  ipaddress <- gsub("\"" , "", ipaddress)
  lastchar <- regexpr("/", ipaddress)
  ipaddress <- substr(ipaddress, 1, lastchar - 1)
}

commit_cpp_change <- function (cpp_image_name, new_cpp_image_name, ipaddress){
  #Copying the container .my.cnf file to current directory of the local host
  systemtext <- paste0("docker cp ",cpp_image_name,":/root/.my.cnf .my.cnf")
  system(systemtext)
  
  local_mycnf_path <- paste0(getwd(),"/.my.cnf")
  
  #Finding which line is host= and writing the new ip addres into that line
  textfile <- readLines(local_mycnf_path)
  hostpos<-grep("host", textfile)
  hostaddress <- textfile[hostpos]
  hostaddress <- strsplit(hostaddress, "=")[[1]]
  print(paste("old .my.cnf IP address:", hostaddress[2]))
  hostaddress[2] <- ipaddress
  textfile[hostpos] <- paste0(hostaddress, collapse = "=")
  writeLines(textfile, con = local_mycnf_path)
  
  #Copying the local host copy of .my.cnf to container root folder
  systemtext2 <- paste0("docker cp ",local_mycnf_path," ",cpp_image_name,":/root/")
  system(systemtext2)
  
  #Deleting the local host copy of .my.cnf
  systemtext3 <- paste0("rm -r ", local_mycnf_path)
  system(systemtext3)
  
  #Saving the container as a new image
  commit_text <- paste("docker commit", cpp_image_name, new_cpp_image_name)
  print(commit_text)
  system(commit_text)
}

bridge <- system("docker network inspect bridge", intern = TRUE)
docker_ps <- system("docker ps", intern = TRUE)

if(any(grepl(mysql_name_input, bridge) == TRUE)){
  new_ipaddress <- get_ipaddress(mysql_name_input)
  print(paste(mysql_name_input, "IP address:", new_ipaddress))
  
  if(any(grepl(cpp_name_input, docker_ps) == TRUE)){
    commit_cpp_change(cpp_name_input, new_cpp_name_input, new_ipaddress)
    print("DONE! New image created!")
  }else{
    print("Incorrect cpp image name.")
  }
}else{
  print("Incorrect mysql container name.")
}
