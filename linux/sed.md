sed -i 's/172.18.85.2/172.18.52.232/g' filename  


sed -i 's/172.18.85.2/172.18.52.232/g' ./km-master/WEB-INF/classes/application-production.yml

grep '172.18.52.232' ./km-master/WEB-INF/classes/application-production.yml 


find . -name "*.yml" | xargs grep '@172.18.85.2'

find . -name "*.yml" | xargs sed -i 's/172.18.85.2/172.18.52.232/g'

find . -name "*.yml" | xargs sed -i 's/@172.18.85.2/@172.18.52.232/g'

sed -i 's/172.18.85.2/172.18.52.232/g' ./NLPAppWS/WEB-INF/classes/jdbc_oracle.xml

find . -name "*.properties" | xargs grep '@172.18.85.2'

find . -name "*.properties" | xargs sed -i 's/@172.18.85.2/@172.18.52.232/g'

find . -name "*.xml" | xargs grep '@172.18.85.2'

find . -name "*.xml" | xargs sed -i 's/@172.18.85.2/@172.18.52.232/g'


find . -name "*.yml" | xargs grep '@172.18.85.2'

find . -name "*.yml" | xargs sed -i 's/@172.18.85.2/@172.18.52.232/g'



find . -name "*test.yml" | xargs sed -i '28i\            keep-alive: true'