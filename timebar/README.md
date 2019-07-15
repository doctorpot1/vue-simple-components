# timebar
Simple Vue component that show you the time and refresh itself.

# Prerequisite
1. moment.js

# Important note
Do remember to import the file and add it as a component. 
`import timebar from '../timebar.vue';`

`export default {
    name: "foo",
    components: {
       timebar
    },
	...
}`

# Sample usage
`<timebar :counter=1000 prefix="Time Now is "></timebar>`

`<timebar :counter=60000 prefix="Last updated at " format="hh:mm A"></timebar>`

`<timebar :counter=60000 prefix="The time now is " format="hh:mm A" suffix=". It is time to sleep!"></timebar>`