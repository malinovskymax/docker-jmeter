0.1. TODO: Generate `rmi_keystore.jks`

1. Build jmeter docker image
    ```shell script
   ./build.sh
    ```

2. Start client ans servers
    ```shell script
   docker-compose up -d --scale server=2
   # docker-compose up -d --scale server=3 --no-recreate
    ```

3. Identify IP addresses of Jmeter servers
    ```shell script
   sudo docker inspect --format '{{ .Name }} => {{ $network := index .NetworkSettings.Networks "docker-jmeter_jmeter" }}{{ $network.IPAddress }}' $(sudo docker ps -a | grep docker-jmeter_server | awk '{print $1}')
    ```

4. Run test on Jmeter client
    ```shell script
   docker exec -it $(sudo docker ps -a | grep docker-jmeter_client | awk '{print $1}') /bin/bash
   
   cd $JMETER_BIN
   export T_DIR=/tests/chime
   export R_DIR=${T_DIR}/report
   rm -rf ${R_DIR} > /dev/null 2>&1
   mkdir -p ${R_DIR}
   /bin/rm -f ${T_DIR}/test-plan.jtl ${T_DIR}/jmeter.log  > /dev/null 2>&1
   
   ./jmeter -Dlog_level.jmeter=DEBUG -n -R172.31.0.3,172.31.0.4 -t ${T_DIR}/consumer-homePageLoad.jmx -l ${T_DIR}/test-plan.jtl -j ${T_DIR}/jmeter.log -e -o ${R_DIR}
    ```
