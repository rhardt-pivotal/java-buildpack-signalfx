#!/bin/bash

BUNDLE_DIR=/home/vcap/app/.java-buildpack/sigfx_agent/collectd
CONFIG_DIR=/home/vcap/app/.signalfx

vcap_app_jq() {
  echo $VCAP_APPLICATION | $BUNDLE_DIR/bin/jq -r $@
}

# The token should be provided in the app manifest as an envvar
export ACCESS_TOKEN=$SIGNALFX_ACCESS_TOKEN
export APP_ID=$(vcap_app_jq '.application_id')
export SFX_DIM_app_id=$(vcap_app_jq '.application_id')
export SFX_DIM_app_name=$(vcap_app_jq '.application_name')
export SFX_DIM_app_instance_index=$(vcap_app_jq '.instance_index')
export SFX_DIM_space_name=$(vcap_app_jq '.space_name')
export DIMS="?app_id=$SFX_DIM_app_id&app_name=$SFX_DIM_app_name&app_instance_index=$SFX_DIM_app_instance_index&space_name=$SFX_DIM_space_name"

echo "****DIMS[$DIMS]"

export ENABLE_JMX=${ENABLE_JMX:-false}

export HOSTNAME=${CF_INSTANCE_IP}

if [[ $SIGNALFX_ENABLE_SYSTEM_METRICS != "yes" ]] && \
   [[ $SIGNALFX_ENABLE_SYSTEM_METRICS != "true" ]]
then
  export NO_SYSTEM_METRICS=true
fi

for conf in $(find $CONFIG_DIR -name "*.conf")
do
  echo "Detected configuration file $(basename $conf)."
  cat $conf | erb > $BUNDLE_DIR/etc/managed_config/$(basename $conf)
done

bash $BUNDLE_DIR/run.sh
