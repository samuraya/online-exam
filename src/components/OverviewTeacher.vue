<template>
	
	<v-container style="height: 1000px;">
		<v-card
		    max-width="500"
		    class="mx-auto"
		  >
		    <v-toolbar
		      :color="'cyan darken-4'"
		      dark
		    >		      
		      <v-toolbar-title>
		        Active Exams
		      </v-toolbar-title>

		      <v-spacer></v-spacer>
		           
		    </v-toolbar>

		    <v-card-text>
		    	<!-- item-value="exam_id" -->
		      <v-select
		      	return-object
		      	v-model="exam"
		        :items="exams"
		        item-text="name"
		        
		        @change="selectExam"
		        :menu-props="{ top: true, offsetY: true }"
		        label="Select exam to edit"
		      ></v-select>

				<v-card-actions
					v-if="selectedExam">
			          <v-spacer></v-spacer>
			           <v-btn
			            dark
			            absolute
			            left
			            color="green"
			            @click="closeExam"
			          >
			            Close Exam
			          </v-btn>
			          <v-btn
			            text
			            color="secondary"
			            @click="cancelAll"
			          >
			            Cancel
			          </v-btn>
			          <v-btn
			            text
			            color="primary"
			            @click="edit"
			          >
			            Edit
			          </v-btn>
		        </v-card-actions>

		    </v-card-text>
	  	</v-card>
	  	<v-divider></v-divider>

	  	<Edit 
	  		v-if="isEdit"
	  		v-bind:exam="exam"
	  	>
	  		
	  	</Edit>

	</v-container>
	
</template>
<script>
	import Edit from '../pages/Edit.vue';
	export default {
		name: 'OverviewTeacher',
		inject: ['ExamService'],
		components: {
			Edit,
		},
		data(){
			return {
				defaultText: 'Select a test',
				selectedExam:false,				
				listOfExams:[],
				exam:{},
				subject:'',
				isEdit: false,
			}
		},
		props : {
			user: {
				type: Object
			},
		},

		methods: {
			selectExam: function(exam){
				
				this.selectedExam=exam['exam_id'];
				this.subject = exam['subject_id'];				
			},
			edit: function(){
				this.isEdit=true;						
			},
			closeExam: function(exam){
								
				this.ExamService.closeExam(this.selectedExam)
				.then((response)=>{
					console.log(response);
				})
				.catch(
					(error)=>console.log(error)
				);
				let index = this.listOfExams.indexOf(exam);
				this.listOfExams.splice(index,1);
				this.selectedExam = false;
				
			},
			cancelAll: function(){
				this.selectedExam=false;
				this.isEdit=false;
			}
		},
		computed: {
			exams(){				
				return this.listOfExams;
			},
		},
		created(){
			Promise.all([this.ExamService.fetchExams()])
				.then(()=>{
					this.listOfExams = this.ExamService.retrieveExams();
				})
				.catch(
					()=>console.log('nothing came back')
				);
		},
	};

</script>
<style scoped>
	.box {
		height: 100%;
	}

</style>