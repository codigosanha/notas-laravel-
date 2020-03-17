<template>
   	<div>
	    <div class="card">
		   	<div class="card-header">
		    	Periodos
		  	</div>
		  	<div class="card-body">
		  		<button type="button" class="btn btn-primary float-right mb-3" data-toggle="modal" data-target="#exampleModal">
				  	Agregar Periodo
				</button>
				<b-button v-b-modal.modal-1>Launch demo modal</b-button>
	
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
		<div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
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
		</div>
		<b-modal id="modal-1" title="BootstrapVue">
		    <p class="my-4">Hello from modal!</p>
		</b-modal>
	</div>
</template>

<script>

	import { es } from 'vuejs-datepicker/dist/locale';
	import Datepicker from 'vuejs-datepicker';
	export default {
		data(){
			return {
				DatePickerFormat: 'yyyy',
				es: es,
				title_modal: 'Registrar Periodo',
				periodos:[],
				anio:'',
				numero:'',
				defaultDate: new Date(),
				periodo_actual: true

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
		      	this.anio =  this.defaultDate.getFullYear();
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
		    }
			
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