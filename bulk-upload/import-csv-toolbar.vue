<template>
    <v-toolbar-items v-if="!setuperror">
        <input ref="csv" name="csv" id="fileUpload" type="file" @change="onFileChange" hidden>
        <v-menu offset-y>
            <template v-slot:activator="{ on }">
                <v-btn flat v-on="on">Import<v-icon>keyboard_arrow_down</v-icon></v-btn>
            </template>
            <v-list>
                <v-list-tile v-on:click="chooseFiles()">
                    <v-list-tile-title>Bulk Upload</v-list-tile-title>
                </v-list-tile>
                <v-list-tile v-on:click="">
                    <v-list-tile-title>
                        <download-csv :data="headers" :name="templateFileName">Download Template</download-csv>
                    </v-list-tile-title>
                </v-list-tile>
            </v-list>
        </v-menu>
    </v-toolbar-items>
</template>

<script>
    import csvparse from 'csv-parse';
    export default {
        name: "import-csv-toolbar",
        data: () => ({
            fileinput: '',
            headers: [],
            setuperror: false
        }),
        computed: {
            options() {
                if (this.remapArray.length != 0) return { skip_empty_lines: true, columns: this.remapArray, from_line: 2 };
                else return { skip_empty_lines: true, columns: true };
            },
        },
        mounted() {
            if(this.template.length != 0) {
                this.headers.push(this.template);
                if(this.remapArray.length != 0 && this.remapArray.length != this.template.length) this.setuperror = true;
            } else this.setuperror = true;
        },
        methods: {
            chooseFiles() { document.getElementById("fileUpload").click() },
            onFileChange(e) {
                var files = e.target.files || e.dataTransfer.files;
                if (!files.length) return;
                //check if it is csv
                var regex = new RegExp("(.*?).(csv)$");
                if (!(regex.test(files[0].name.toLowerCase()))) {
                    this.$emit('error','Please select correct file format');
                } else this.createInput(files[0]);
            },
            createInput(file) {
                var reader = new FileReader();
                var vm = this;
                reader.onload = (e) => {
                    vm.fileinput = reader.result;
                    var firstLine = reader.result.split('\r\n').shift();
                    document.getElementById("fileUpload").value = "";
                    if(this.checkCSVHeader(firstLine)) {
                        csvparse(this.fileinput, this.options, function(err, output){
                            if(err) vm.$emit('error', 'Error with file');
                            else vm.$emit('success', output);
                        })
                    } else { vm.$emit('error', "Error with file. Headers doesn't match template's header."); }

                }
                reader.readAsText(file);
            },
            checkCSVHeader(headerText) {
                try {
                    let headers = headerText.split(",");
                    for (var index = 0; index < headers.length; index++) {
                        if(this.template[index] != headers[index]) return false;
                    }
                    return true;
                } catch (e) {
                    return false;
                }
            }
        },
        props: {
            template: {
                type: Array,
                required: true
            },
            templateFileName: {
                type: String,
                default() { return "template.csv"; }
            },
            remapArray: {
                type: Array,
                default() { return []; }
            }
        }
    }
</script>

<style scoped>

</style>