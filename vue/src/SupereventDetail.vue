<template>
    <div>
        <gravitational-wave-banner :supereventData="superevent_data" />
        <b-row>
            <b-col cols="6">
                <alerts-table :alerts="alerts" :skipApiBaseUrl="skipApiBaseUrl"></alerts-table>
            </b-col>
            <b-col cols="3">
                <b-img src="https://gracedb.ligo.org/api/superevents/S190426c/files/bayestar.volume.png" fluid></b-img>
            </b-col>
            <b-col cols="3">
                <b-img src="https://gracedb.ligo.org/api/superevents/S190426c/files/bayestar.png" fluid></b-img>
            </b-col>
        </b-row>
    </div>
</template>

<script>
import axios from 'axios';
import { AlertsTable, GravitationalWaveBanner } from '@/components';

export default {
    name: 'SupereventDetail',
    components: {
        AlertsTable,
        GravitationalWaveBanner
    },
    data: function() {
        return {
            alerts: [],
            alert_fields: [
                { 'key': 'identifier' },
                { 'key': 'timestamp', 'sortable': true },
                { 'key': 'from' },
                { 'key': 'subject' }
            ],
            superevent_identifier: undefined,
            superevent_data: {}
        }
    },
    props: {
        tomApiBaseUrl: {
            type: String,
            required: true
        },
        skipApiBaseUrl: {
            type: String,
            required: true
        }
    },
    mounted() {
        console.log('mounted SupereventDetail.vue');
        this.superevent_identifier = document.getElementById('superevent_id').textContent;
        axios
            .get(`${this.skipApiBaseUrl}/api/events/?identifier=${this.superevent_identifier}`)
            .then(response => {
                this.superevent_data = response['data']['results'][0];
                axios
                    .get(`${this.skipApiBaseUrl}/api/events/${response['data']['results'][0]['id']}`)
                    .then(alert_response => {
                        this.alerts = alert_response['data']['alerts'];
                    })
                    .catch(
                        error => {
                            console.log(`Error getting alerts for superevent ${this.superevent_identifier}: ${error}`);
                        }        
                    )
            })
            .catch(
                error => {
                    console.log(`Error getting details for superevent ${this.superevent_identifier}: ${error}`);
                }
            )
    }
}
</script>

<style scoped>

</style>