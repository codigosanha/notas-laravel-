<template>
   	<div>
	    <div class="card">
		   	<div class="card-header">
		    	Periodos
		  	</div>
		  	<div class="card-body">
		  		<!-- <button type="button" class="btn btn-primary float-right mb-3" data-toggle="modal" data-target="#exampleModal">
		  						  	Agregar Periodo
		  						</button> -->
		  		<b-row class="mb-2">
		  			<b-col>
			  			
					    <b-button v-b-modal.modal-prevent-closing>Agregar Périodo</b-button>
				    	
				    </b-col>
				</b-row>

				
	
				<table class="table table-bordered">
					<thead>
						<th>Año</th>
						<th>Numero</th>
						<th>Periodo Actual</th>
						<th>Acciones</th>
					</thead>
					<tbody>
						<tr v-for="periodo in periodos">
							<td>{{ periodo.anio}}</td>
							<td>{{ periodo.numero}}</td>
							<td>
								<span class="badge badge-success" v-if="periodo.estado"> SI </span>
								<span class="badge badge-danger" v-else="periodo.estado"> NO </span>
							</td>
							<td>{{ periodo.anio}}</td>
						</tr>
					</tbody>
				</table>
		    	
		  	</div>
		</div>
		<!-- <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
		  	<div class="modal-dialog" role="document">
		    	<div class="modal-content">
		      		<div class="modal-header">
		        		<h5 class="modal-title" id="exampleModalLabel" v-text="title_modal"></h5>
		        		<button type="button" class="close" data-dismiss="modal" aria-label="Close">
		          			<span aria-hidden="true">&times;</span>
		        		</button>
		      		</div>
		      		<form @submit.prevent="savePeriodo">
			      	<div class="modal-body">
			      		
			        	<div class="form-group">
			        		<label for="">Año:</label>
							<datepicker :language="es" :format="DatePickerFormat" minimum-view="year" :bootstrap-styling="true"  v-model="defaultDate" @closed="convertDate"></datepicker>
			        	</div>
			        	<div class="form-group">
			        		<label for="">Número:</label>
							<select v-model="numero" id="numero" class="form-control" required="required">
								<option value="I">I</option>
								<option value="II">II</option>
							</select>
			        	</div>
			        	<div class="form-group">
			        		<label class="checkbox-inline"><input type="checkbox" v-model="periodo_actual" :true-value="true"
		  							:false-value="false">Marcar como Activo</label>
			        	</div>
			      	</div>
			      	<div class="modal-footer">
			        	<button type="button" class="btn btn-secondary" data-dismiss="modal">Cerrar</button>
			        	<button type="submit" class="btn btn-primary">Guardar</button>
			      	</div>
			      	</form>
		    	</div>
		  	</div>
		</div> -->
		<b-modal
	      	id="modal-prevent-closing"
	      	ref="modal"
	      	title="Submit Your Name"
	      	@show="resetModal"
	      	@hidden="resetModal"
	      	@ok="handleOk"
	      	hide-footer
	    >
	      	<b-form @submit.stop.prevent="onSubmit">
			    <b-form-group id="example-input-group-1" label="Año" label-for="example-input-1">
			        <datepicker :language="es" :format="DatePickerFormat" minimum-view="year" :bootstrap-styling="true"  v-model="form.defaultDate" @closed="convertDate" :state="validateState('defaultDate')" aria-describedby="defaultDate"></datepicker>

			        <b-form-invalid-feedback
			          	id="defaultDate"
			        >
			    		This is a required field and must be at least 3 characters.
			    	</b-form-invalid-feedback>
			    </b-form-group>

			    <b-form-group id="example-input-group-2" label="Número" label-for="numero">
			        <b-form-select
			          	id="numero"
			          	name="numero"
			          	v-model="$v.form.numero.$model"
			          	:options="numeros"
			          	:state="validateState('numero')"
			          	aria-describedby="numero"
			        ></b-form-select>
			        <b-form-invalid-feedback id="numero">This is a required field.</b-form-invalid-feedback>
			    </b-form-group>
			    <b-form-group id="example-input-group-2" label="Periodo Actual" label-for="periodo_actual">
			        <b-form-checkbox
				      	id="periodo_actual"
				      	v-model="form.periodo_actual"
				      	name="periodo_actual"
				      	value="true"
				      	unchecked-value="false"
				    >
				      	Marcar como périodo Actual				    
				    </b-form-checkbox>
			    </b-form-group>

      			<b-button type="submit" variant="primary">Submit</b-button>
      			<b-button class="ml-2" @click="resetForm()">Reset</b-button>
    		</b-form>
	    </b-modal>
	</div>
</template>

<script>

	import { es } from 'vuejs-datepicker/dist/locale';
	import Datepicker from 'vuejs-datepicker';
	import { validationMixin } from "vuelidate";
	import { required, minLength } from "vuelidate/lib/validators";


	export default {
		mixins: [validationMixin],
		data(){
			return {
				DatePickerFormat: 'yyyy',
				es: es,
				title_modal: 'Registrar Periodo',
				periodos:[],
        		numeros: [
		          { value: null, text: 'Please select an option' },
		          { value: 'I', text: 'I' },
		          { value: 'II', text: 'II' },
		        ],
		        form:{
		        	anio:null,
		        	periodo_actual:null,
		        	numero:null,
		        	defaultDate: new Date()
		        }
			}
		},
		validations: {
		    form: {
		      	defaultDate: {
		        	required
		      	},
		      	numero: {
		        	required,
		      	}
		    }
		},
		methods:{
			getPeriodos: function(){
				axios.get('/periodos')
					.then(resp=>{
						this.periodos = resp.data;
					}).catch(error=>{
						console.log(error);
					});
			},
			convertDate: function () {
		      	// `this` points to the vm instance
		      	this.form.anio =  this.form.defaultDate.getFullYear();
		    },
		    savePeriodo(){
		    	axios.post('/periodos',{
		    		periodo_actual: this.periodo_actual,
		    		anio: this.anio,
		    		numero: this.numero,
	    		})
				.then(resp=>{
					console.log(resp);
				}).catch(error=>{
					console.log(error);
				});
		    },
		 
	       	validateState(name) {
		      	const { $dirty, $error } = this.$v.form[name];
		      	return $dirty ? !$error : null;
		    },
		    resetForm() {
		      	this.form = {
		        	name: null,
		        	food: null
		      	};

		      	this.$nextTick(() => {
		        	this.$v.$reset();
		      	});
		    },
		    onSubmit() {
		      	this.$v.form.$touch();
		      	if (this.$v.form.$anyError) {
		        	return;
		      	}
		      	alert("Form submitted!");
		  	},
		  	resetModal() {
		        this.name = ''
		        this.nameState = null
		    },
		    handleOk(bvModalEvt) {
		        // Prevent modal from closing
		        bvModalEvt.preventDefault()
		        // Trigger submit handler
		        this.handleSubmit()
		    },
		},
	
		mounted(){
			this.getPeriodos();
			this.convertDate();
		},
		components: {
		    Datepicker
		}
	}
</script>
<style>
	.vdp-datepicker .input-group .form-control[readonly] {
background: none !important;
}
</style>