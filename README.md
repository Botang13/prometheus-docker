# Agar Docker daemon menjadi 'target' dari Prometheus, perlu menentukan metrics-address via daemon.json
	
#Lokasi File :
	
	Linux : /etc/docker/daemon.json
	Windows : C:\ProgramData\docker\config\daemon.json

#Isi dari daemon.json :
	
	{
	  "metrics-addr" : "localhost:9323",
	  "experimental" : true
	}

#Config dari prometheus.yml

	#my global config
  
	global:
    	  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
    	  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
    	  # scrape_timeout is set to the global default (10s).

    	  # Attach these labels to any time series or alerts when communicating with
    	  # external systems (federation, remote storage, Alertmanager).
    	  external_labels:
     	   monitor: 'docker-monitor'

	#Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
  
  	rule_files:
    	  # - "first.rules"
    	  # - "second.rules"

	#A scrape configuration containing exactly one endpoint to scrape:
	#Here it's Prometheus itself.
  	
	scrape_configs:
    	# The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
    	- job_name: 'prometheus'

	        # metrics_path defaults to '/metrics'
      	  	# scheme defaults to 'http'.

        static_configs:
          - targets: ['localhost:9090']

    	- job_name: 'docker'
          # metrics_path defaults to '/metrics'
          # scheme defaults to 'http'.

      	static_configs:
       	  - targets: ['localhost:9323']

#Lokasi File :

  	Linux : /etc/prometheus/prometheus.yml

#Buat Docker Swarm
  
  	docker swarm init
  	docker service create --replicas 1 --name my-prometheus \
    	--mount type=bind,source=/tmp/prometheus.yml,destination=/etc/prometheus/prometheus.yml \
    	--publish published=9090,target=9090,protocol=tcp \
    	prom/prometheus

#Setelah selesai cek di 

	http://localhost:9090/targets/
	
#Jika muncul 'docker' dan statenya up maka berhasil

#Happy Monitoring ^^
