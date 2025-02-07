
instead of doing 

git checkout -b <mybranchName> upstream/main 

i ended up doing

git checkout -b upstream/main 



and then next time when i tried 

git checkout -b <mybranchName> upstream/main  

it showed me the error ->  'upstream/0.0.4274-ebf' is ambiguous.  

Soln : 

Went to the bottom right , there was a new folder created upstream , and then i opened it and clicked on delete branch within that folder and rest was fine 
