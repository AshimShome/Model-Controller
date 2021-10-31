####################### For Model #############################
 protected $guarded = [];
    public function department()
    {
        return $this->belongsTo(Department::class);
    }
}

####################### For Table Schema #############################

Schema::create('employees', function (Blueprint $table) {
            $table->id();
            $table->bigInteger('department_id');
            $table->string('employee_office_id');
            $table -> string('employee_name');
            $table -> string('email_id' , 40) -> unique() ;
            $table->string('employee_img')->nullable();
            $table->string('employee_phone');
            $table->string('employee_fathers_name');
            $table->string('employee_mother_name');
            $table->string('employee_present_address');
            $table->string('employee_permanent_address');
            $table->integer('employee_salary');
            $table->string('designation');
            $table->date('employee_date_of_birth');
            $table->date('employee_joing_date');
            $table->integer('employee_status')->default(1);
            $table->timestamps();
        });

####################### For Controller #############################


<?php

namespace App\Http\Controllers\Backend;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use App\Models\Department;
use App\Models\Employee;
use Image;

class EmployeeController extends Controller 
{


  // employee view
  public function EmployeeView(){
    $employees =Employee::all();
   
    return view('backend.employee.manage_employee',compact('employees'));

} // end mathod

      // Employee Add form
      public function EmployeeAddForm(){

        $departments=Department::all();
        return view('backend.employee.add_employee',compact('departments'));

    }// end method

       // Employee Add
       public function EmployeeStore(Request $request){
      //  dd($request->employee_photo);
      

       $request->validate([
        'employee_name' => 'required',
        'department_id' => 'required',
        'employee_office_id' => 'required',
        'employee_photo' => 'required',
        'email_id' => "required|unique:employees",
        'employee_phone' => 'required',
        'employee_fathers_name' => 'required',
        'employee_mother_name' => 'required',
        'employee_salary' => 'required',
        'employee_date_of_birth' => 'required',
        'employee_joing_date' => 'required',
        'employee_present_address' => 'required',
        'employee_permanent_address' => 'required',
        'designation' => 'required',

       ]);
    
       // Banner Img upload and save
       $image = $request->file('employee_photo');
       $name_gen = hexdec(uniqid()).'.'.$image->getClientOriginalExtension();
       Image::make($image)->resize(870,370)->save('upload/employee/'.$name_gen );
       $save_url = 'upload/employee/'.$name_gen;


    
    
      Employee::insert([

        'employee_name' => $request->employee_name,
        'department_id' => $request->department_id,
        'employee_office_id' => $request->employee_office_id,
        'employee_status' => $request->employee_status,
        'employee_img' => $save_url,
        'designation' => $request->designation,
        'email_id' => $request->email_id,
        'employee_phone' => $request->employee_phone,
        'employee_fathers_name' => $request->employee_fathers_name,
        'employee_mother_name' => $request->employee_mother_name,
        'employee_salary' => $request->employee_salary,
        'employee_date_of_birth' => $request->employee_date_of_birth,
        'employee_joing_date' => $request->employee_joing_date,
        'employee_present_address' => $request->employee_present_address,
        'employee_permanent_address' => $request->employee_permanent_address,

            ]);
    
    
            $notification = array(
                'message' =>'Employee Create sucessfully',
                'alert-type' =>'success'
            );
    
            return redirect()->back()->with($notification);

    }// end method 


  // employee Edit
  public function EmployeeEdit($id){

    $employee = Employee::find($id); 
    $departments=Department::all();  
    return view('backend.employee.edit_employee',compact('employee','departments'));

} // end mathod
    

  // update employee
  public function EmployeeUpdate(Request $request){
    
    $employee_id = $request->id;
    $old_img  = $request->old_img;

    if ($request->file('employee_photo')) {

        unlink($old_img);
        $image = $request->file('employee_photo');
        $name_gen = hexdec(uniqid()).'.'.$image->getClientOriginalExtension();
        Image::make($image)->resize(300,300)->save('upload/employee/'.$name_gen);
        $save_url = 'upload/employee/'.$name_gen;

     // employee Update    
     Employee::findOrFail($employee_id)->update([
      'employee_name' => $request->employee_name,
      'department_id' => $request->department_id,
      'employee_office_id' => $request->employee_office_id,
      'employee_status' => $request->employee_status,
      'employee_img' => $save_url,
      'email_id' => $request->email_id,
      'employee_phone' => $request->employee_phone,
      'employee_fathers_name' => $request->employee_fathers_name,
      'employee_mother_name' => $request->employee_mother_name,
      'employee_salary' => $request->employee_salary,
      'employee_date_of_birth' => $request->employee_date_of_birth,
      'employee_joing_date' => $request->employee_joing_date,
      'employee_present_address' => $request->employee_present_address,
      'employee_permanent_address' => $request->employee_permanent_address,



        ]);

        $notification = array(
          'message' =>  'Employee Update Sucessyfuly',
          'alert-type' => 'info'
      ); 

      return redirect()->route('employee.view')->with($notification);


    }else{
      Employee::findOrFail($employee_id)->update([
        'employee_name' => $request->employee_name,
        'department_id' => $request->department_id,
        'employee_office_id' => $request->employee_office_id,
        'employee_status' => $request->employee_status,
        'email_id' => $request->email_id,
        'employee_phone' => $request->employee_phone,
        'employee_fathers_name' => $request->employee_fathers_name,
        'employee_mother_name' => $request->employee_mother_name,
        'employee_salary' => $request->employee_salary,
        'employee_date_of_birth' => $request->employee_date_of_birth,
        'employee_joing_date' => $request->employee_joing_date,
        'employee_present_address' => $request->employee_present_address,
        'employee_permanent_address' => $request->employee_permanent_address,
 
           ]);
 
           $notification = array(
             'message' =>  'Employee Update Sucessyfuly',
             'alert-type' => 'info'
         ); 
 
         return redirect()->route('employee.view')->with($notification);
 

    } // else end

} // method end  

  // employee All  view
  public function EmployeeDetails($id){
   
    $emp = Employee::find($id); 
  

    return view('backend.employee.all_view_employee',compact('emp'));

} // end mathod  EmployeetDelete

public function EmployeetDelete($id){

  $employee = Employee::findOrFail($id);

  $img = $employee->employee_img;
  unlink($img);
  Employee::findOrFail($id)->delete();
  
  $notification = array(
  'message' =>  'Employee Delete Sucessyfully',
  'alert-type' => 'info'
); 

return redirect()->back()->with($notification);

}

}
      
 
####################### For Route #############################



use App\Http\Controllers\Backend\EmployeeController;
###########
//  Employee Route Group
Route::prefix('employee')->group(function(){
    Route::get('/view', [EmployeeController::class, 'EmployeeView'])->name('employee.view');
    Route::get('/addform', [EmployeeController::class, 'EmployeeAddForm'])->name('employee.addform');
    Route::post('/store', [EmployeeController::class, 'EmployeeStore'])->name('employee.store');

 // Edit Employee
Route::get('/edit/{id}', [EmployeeController::class, 'EmployeeEdit'])->name('employee.edit');
// Upadte Employee
Route::post('/update', [EmployeeController::class, 'EmployeeUpdate'])->name('employee.update');

  //Employee Full View
  Route::get('/details/{id}', [EmployeeController::class, 'EmployeeDetails'])->name('employee.details');

  Route::get('/delete/{id}', [EmployeeController::class, 'EmployeetDelete'])->name('employee.delete');



});


####################### For View #############################


####################### For Manage employee #############################

@extends('admin.admin_master')

{{-- section id is yeild name  --}}

@section('admin')

<div class="container-full"> 
 <section class="content">

<div class="row">
    <div class="col-lg-12">

       <div class="box">
         <div class="box-header with-border">
           <h3 class="box-title">Data Table With Full Features</h3>
         </div>
         <!-- /.box-header -->
         <div class="box-body">
             <div class="table-responsive">
               <table id="example1" class="table table-bordered table-striped">
                 <thead>
                     <tr>
                         <th>Employee ID</th>
                         <th>Employee Name</th>
                         <th>Employee Phone</th>
                         <th>Employee Profile Image</th>
                         <th>Action</th>
                        
                     </tr>
                 </thead>
                 <tbody>

                   @foreach ($employees as $employee)
                   <tr>
                       <td>{{ $employee->id }}</td>
                       <td>{{ $employee->employee_name }}</td>
                       <td>{{ $employee->employee_phone }}</td>
                       <td>                          
                         <img src="{{ asset($employee->employee_img) }}" style="height:40px; width:70px;">
                                                  
                      
                       </td>
                       <td>
                        <a href="{{ route('employee.details', $employee->id )}}" class="btn btn-success">View</a> 

                           <a href="{{ route('employee.edit', $employee->id ) }}" class="btn btn-warning">Edit</a> 

                           <a href="{{ route('employee.delete', $employee->id ) }}" class="btn btn-danger" id="#">Delete</a>
                       </td>  
                       
                       
                       
                   </tr>  
                   @endforeach --> 
                     
                     
                 </tbody>
             
               </table>
               </div> <!-- table res.. end -->
             </div>  <!-- box body end -->      
          </div>      <!-- box end -->       
     </div> <!-- col end --> 



</div> <!--  row end-->
</section> <!--  content end-->
</div> <!--  row end-->


@endsection


####################### For Add employee #############################


 
@extends('admin.admin_master')
@section('admin')


<div class="container-full">
    <!-- Content Header (Page header) -->


            <!-- Main content -->
            <section class="content">

            <!-- Basic Forms -->
            <div class="box">
                <div class="box-header with-border">
                <h4 class="box-title">Add Employee </h4>

                </div>
                <!-- /.box-header -->
                <div class="box-body">
                <div class="row">
                    <div class="col">


              <form action="{{route('employee.store')}}" method="POST" enctype="multipart/form-data">
                @csrf
                <div class="row">
                 <div class="col-12">	


            <div class="row"> <!-- start 1st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Name</h5>
                        <div class="controls">
                        <input type="text" id="employee_name" name="employee_name" class="form-control" required> 
                        @error('employee_name')
                        <strong>{{ $message }}</strong>	
                    @enderror
                       
                       </div>
                        </div> 

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Department Name</h5>
                        <div class="controls">
                            <select id="department_id" name="department_id"class="form-control" required>
                                <option value=" ">Select Department Name</option>
                                @foreach ($departments as $department)
                                <option value="{{$department->id}}">{{$department->department_name}}</option>

                                @endforeach
                                
                             </select>                        
                              @error('department_id')
                        <strong>{{ $message }}</strong>	
                    @enderror
                       
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Office Id</h5>
                        <div class="controls">
                        <input type="text" id="employee_office_id" name="employee_office_id" class="form-control" required> 
                        @error('employee_office_id')
                        <strong>{{ $message }}</strong>	
                        
                    @enderror
                       
                       </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 1st row  -->



            <div class="row"> <!-- start 2st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                       <h5> Employee Profile Image <span class="text-danger">*</span></h5>
                       <div class="controls">
                       <input type="file" id="employee_photo" name="employee_photo" class="form-control" required > 
                        
                      
                       @error('employee_photo')
                       <strong>{{ $message }}</strong>	
                       
                   @enderror
                      </div>
                    </div>

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Email Id</h5>
                        <div class="controls">
                        <input type="email" id="email_id" name="email_id" class="form-control" required> 
                        @error('email_id')
                        <strong>{{ $message }}</strong>	
                       
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Stutus</h5>
                            <div class="controls">
                           <select id="employee_status" name="employee_status"class="form-control">
                                <option value="1">Select Employee Stutus</option>
                                <option value="1">Active</option>
                                <option value="0">In Active </option>
                                
                             </select>                            
                             @error('employee_status')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 2st row  -->

            <div class="row"> <!-- start 3st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Phone</h5>
                        <div class="controls">
                        <input type="number" id="employee_phone" name="employee_phone" class="form-control" required> 
                        @error('employee_phone')
                        <strong>{{ $message }}</strong>	
                        
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Fathers Name</h5>
                        <div class="controls">
                        <input type="text" id="employee_fathers_name" name="employee_fathers_name" class="form-control" required> 
                        @error('employee_fathers_name')
                        <strong>{{ $message }}</strong>	
                       
                    @enderror
                       </div>
                        </div>    

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Mother Name</h5>
                        <div class="controls">
                        <input type="text" id="employee_mother_name" name="employee_mother_name" class="form-control" required> 
                        @error('employee_mother_name')
                        <strong>{{ $message }}</strong>	
                       
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->

            </div> <!-- end 3st row  -->

            <!-- start 4st row  -->
            <div class="row"> 
                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Salary</h5>
                        <div class="controls">
                        <input type="number" id="employee_salary" name="employee_salary" class="form-control" required> 
                        @error('employee_salary')
                        <strong>{{ $message }}</strong>	
                        
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Date Of Birth</h5>
                            <div class="controls">
                            <input type="date" value="2018-05-10 00:00:00" class="form-control" name="employee_date_of_birth"value="{{old('dob')}}" id="employee_date_of_birth" placeholder="Enter Your Date Of Birth">
                            @error('employee_date_of_birth')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        </div>   

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Joing Date</h5>
                            <div class="controls">
                            <input type="date" value="2018-05-10 00:00:00" class="form-control" name="employee_joing_date"value="{{old('dob')}}" id="employee_joing_date" placeholder="Enter Your Date Of Birth">
                            @error('employee_date_of_birth')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        </div> 

                </div> <!-- end col md 4 -->

            </div> <!-- end 4st row  -->

            <!-- start 5st row  -->
            <div class="row">
                <div class="col-md-4">
                    <div class="form-group" >
                        <div style="display: flex; flex-direction:column;">
                         <h5> <span class="text-danger">*</span> Employee Designation</h5>
                              <input type="text"  id="designation" class="form-control"name="designation" >
                        </div>
                         @error('designation')
                         <span class="text-danger">{{ $message }}</span>
                         @enderror
                    </div> 

                </div> <!-- end col md 4 -->
                <div class="col-md-4">
                    <div class="form-group" >
                        <div style="display: flex; flex-direction:column;">
                         <h5> <span class="text-danger">*</span> Employee Present Address</h5>
                              <textarea  id="employee_present_address" class="form-control"name="employee_present_address" rows="4"  cols="70"></textarea> 
                        </div>
                         @error('employee_present_address')
                         <span class="text-danger">{{ $message }}</span>
                         @enderror
                    </div> 

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group" >
                        <div style="display: flex; flex-direction:column;">
                         <h5> <span class="text-danger">*</span> Employee Permanent Address</h5>
                              <textarea  id="employee_permanent_address" class="form-control"name="employee_permanent_address" rows="4"  cols="70"></textarea> 
                        </div>
                         @error('employee_permanent_address')
                         <span class="text-danger">{{ $message }}</span>
                         @enderror
                    </div>   

                </div> <!-- end col md 4 -->

            </div>
                 <!-- end 5st row  -->

        

      <br>
       <br>
       <br>

          <div class="text-xs-right">
            <input type="submit" class="btn btn-rounded btn-primary mb-5" value="Add Employee">
                            </div>
                        </form>

                    </div>
                    <!-- /.col -->
                </div>
                <!-- /.row -->
                </div>
                <!-- /.box-body -->
            </div>
            <!-- /.box -->

            </section>
            <!-- /.content -->
        </div>


@endsection

 

####################### For Edit employee #############################


 
@extends('admin.admin_master')
@section('admin')


<div class="container-full">
    <!-- Content Header (Page header) -->


            <!-- Main content -->
            <section class="content">

            <!-- Basic Forms -->
            <div class="box">
                <div class="box-header with-border">
                <h4 class="box-title">Update Employee </h4>

                </div>
                <!-- /.box-header -->
                <div class="box-body">
                <div class="row">
                    <div class="col">


              <form action="{{route('employee.update')}}" method="POST" enctype="multipart/form-data">
                @csrf
                <input type="hidden" name="id" value="{{ $employee->id }}">        
                <input type="hidden" name="old_img" value="{{ $employee->employee_img}}">


                {{-- <img src="{{ asset($employee->employee_img) }}" style="height:40px; width:70px;"> --}}
    

                <div class="row">
                 <div class="col-12">	


            <div class="row"> <!-- start 1st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Name</h5>
                        <div class="controls">
                        <input type="text" id="employee_name" name="employee_name" value="{{ $employee->employee_name}}" class="form-control" required> 
                        @error('employee_name')
                        <strong>{{ $message }}</strong>	
                    @enderror
                       
                       </div>
                        </div> 

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Department Name</h5>
                        <div class="controls">
                            <select id="department_id" name="department_id"class="form-control" required>
                                <option value=" ">Select Department Name</option>
                                @foreach ($departments as $department)
                                    @if ($employee->department_id==$department->id)
                                    <option value="{{$department->id}}" selected>{{$department->department_name}}</option>
                                    @else
                                    <option value="{{$department->id}}">{{$department->department_name}}</option>

                                    @endif

                                @endforeach
                                
                             </select>                        
                              @error('department_id')
                        <strong>{{ $message }}</strong>	
                    @enderror
                       
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Office Id</h5>
                        <div class="controls">
                        <input type="text" id="employee_office_id" value="{{ $employee->employee_office_id}}" name="employee_office_id" class="form-control" required> 
                        @error('employee_office_id')
                        <strong>{{ $message }}</strong>	
                        
                    @enderror
                       
                       </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 1st row  -->



            <div class="row"> <!-- start 2st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                       <h5> Employee Profile Image <span class="text-danger">*</span></h5>
                       <div class="controls">
                       <input type="file" id="employee_photo" name="employee_photo" class="form-control"  > 
                        
                      
                       @error('employee_photo')
                       <strong>{{ $message }}</strong>	
                       
                   @enderror
                      </div>
                    </div>

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Email Id</h5>
                        <div class="controls">
                        <input type="email" id="email_id" name="email_id"value="{{ $employee->email_id}}"  class="form-control" required> 
                        @error('email_id')
                        <strong>{{ $message }}</strong>	
                       
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Stutus</h5>
                            <div class="controls">
                           <select id="employee_status" name="employee_status"class="form-control">
                                <option value="1">Select Employee Stutus</option>
                              
                                <option {{($employee->employee_status==1? 'selected':'') }} value="1">Active</option>
                                
                                <option {{($employee->employee_status==0? 'selected':'') }}  value="0">In Active </option>
                               
                             </select>                            
                             @error('employee_status')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 2st row  -->

            <div class="row"> <!-- start 3st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Phone</h5>
                        <div class="controls">
                        <input type="number" id="employee_phone"value="{{ $employee->employee_phone}}" name="employee_phone" class="form-control" required> 
                        @error('employee_phone')
                        <strong>{{ $message }}</strong>	
                        
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Fathers Name</h5>
                        <div class="controls">
                        <input type="text" id="employee_fathers_name"value="{{ $employee->employee_fathers_name}}" name="employee_fathers_name" class="form-control" required> 
                        @error('employee_fathers_name')
                        <strong>{{ $message }}</strong>	
                       
                    @enderror
                       </div>
                        </div>    

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Mother Name</h5>
                        <div class="controls">
                        <input type="text" id="employee_mother_name"value="{{ $employee->employee_mother_name}}" name="employee_mother_name" class="form-control" required> 
                        @error('employee_mother_name')
                        <strong>{{ $message }}</strong>	
                       
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->

            </div> <!-- end 3st row  -->

            <!-- start 4st row  -->
            <div class="row"> 
                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Salary</h5>
                        <div class="controls">
                        <input type="number" id="employee_salary"value="{{ $employee->employee_salary}}" name="employee_salary" class="form-control" required> 
                        @error('employee_salary')
                        <strong>{{ $message }}</strong>	
                        
                    @enderror
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Date Of Birth</h5>
                            <div class="controls">
                            <input type="date" value="{{ $employee->employee_date_of_birth}}" class="form-control" name="employee_date_of_birth"value="{{old('dob')}}" id="employee_date_of_birth" placeholder="Enter Your Date Of Birth">
                            @error('employee_date_of_birth')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        </div>   

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <span class="text-danger">*</span> Employee Joing Date</h5>
                            <div class="controls">
                            <input type="date" value="{{ $employee->employee_joing_date}}"class="form-control" name="employee_joing_date"value="{{old('dob')}}" id="employee_joing_date" placeholder="Enter Your Date Of Birth">
                            @error('employee_date_of_birth')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        </div> 

                </div> <!-- end col md 4 -->

            </div> <!-- end 4st row  -->

            <!-- start 5st row  -->
            <div class="row">
                <div class="col-md-6">
                    <div class="form-group" >
                        <div style="display: flex; flex-direction:column;">
                         <h5> <span class="text-danger">*</span> Employee Present Address</h5>
                              <textarea  id="employee_present_address" class="form-control"name="employee_present_address" rows="4"  cols="70">
                                {{$employee->employee_present_address}}
                            </textarea> 
                        </div>
                         @error('employee_present_address')
                         <span class="text-danger">{{ $message }}</span>
                         @enderror
                    </div> 

                </div> <!-- end col md 4 -->


                
                <div class="col-md-6">

                    <div class="form-group" >
                        <div style="display: flex; flex-direction:column;">
                         <h5> <span class="text-danger">*</span> Employee Permanent Address</h5>
                              <textarea  id="employee_permanent_address"
                               class="form-control"name="employee_permanent_address" rows="4"  cols="70">
                            {{$employee->employee_permanent_address}}
                            </textarea> 
                        </div>
                         @error('employee_permanent_address')
                         <span class="text-danger">{{ $message }}</span>
                         @enderror
                    </div>   

                </div> <!-- end col md 4 -->

            </div>
                 <!-- end 5st row  -->

        

      <br>
       <br>
       <br>

          <div class="text-xs-right">
            <input type="submit" class="btn btn-rounded btn-primary mb-5" value="Update Employee">
                            </div>
                        </form>

                    </div>
                    <!-- /.col -->
                </div>
                <!-- /.row -->
                </div>
                <!-- /.box-body -->
            </div>
            <!-- /.box -->

            </section>
            <!-- /.content -->
        </div>


@endsection

 

####################### For single employee details #############################



@extends('admin.admin_master')
@section('admin')


<div class="container-full">
    <!-- Content Header (Page header) -->


            <!-- Main content -->
            <section class="content">

            <!-- Basic Forms -->
            <div class="box">
                <div class="box-header with-border">
                <h4 class="box-title">View Employee </h4>

                </div>
                <!-- /.box-header -->
                <div class="box-body">
                <div class="row">
                    <div class="col">


              <form action="#" method="POST" enctype="multipart/form-data">
                @csrf
               {{-- {{dd($emp)}} --}}

                <div class="row">
                 <div class="col-12">	


                    <div class="row"> <!-- start Image row  -->
                        <div class="col-md-4">
        
                            <div class="form-group">
                                <div class="controls">
                               </div>
                                </div> 
        
                        </div> <!-- end col md 4 -->
        
        
                        
                        <div class="col-md-4">
        
                            <div class="form-group">
                            
                                <img src="{{ asset($emp->employee_img) }}" style="height:50%; border-radius: 50%; width:50%; border: 2px solid rgb(253, 251, 251);">
                                <div class="controls">
                                  
                               
                               </div>
                                </div>  
        
                        </div> <!-- end col md 4 -->
        
        
                        <div class="col-md-4">
                            <div class="form-group">
                                <div class="controls">
                               </div>
                                </div>
        
                        </div> <!-- end col md 4 -->
        
                    </div> <!-- end Image row  -->


            <div class="row"> <!-- start 1st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5>  <b>Employee Name :</b>{{$emp->employee_name}}</h5>
                        <div class="controls">
                       </div>
                        </div> 

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5><b> Department Name :</b> {{$emp->department->department_name}}</h5>
                        <div class="controls">
                          
                       
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <b>Employee Office Id :</b> {{ $emp->employee_office_id}}</h5>
                        <div class="controls">
                       </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 1st row  -->


            <div class="row"> <!-- start 2st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <b> Employee Email Id :</b> {{ $emp->email_id}}</h5>
                        <div class="controls">
                       </div>
                        </div> 

                </div> <!-- end col md 4 -->


                
                <div class="col-md-4">

                    <div class="form-group">
                        <h5><b>Employee Stutus : </b>
                            @if($emp->employee_status==1)
                            Active
                            @else 
                            In Active 
                            @endif

                        </h5>
                        <div class="controls">
                          
                       
                       </div>
                        </div>  

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <b>Employee Phone :</b> {{ $emp->employee_phone}}</h5>
                        <div class="controls">
                       </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 2st row  -->


            <div class="row"> <!-- start 3st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <b> Employee Fathers Name : </b>{{ $emp->employee_fathers_name}}</h5>
                        <div class="controls">
                       </div>
                        </div> 

                </div> <!-- end col md 4 -->

                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <b>Employee Mother Name :</b> {{ $emp->employee_mother_name}}</h5>
                        <div class="controls">
                       </div>
                        </div> 

                </div> <!-- end col md 4 -->


                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <b>Employee Salary :</b>{{ $emp->employee_salary}}</h5>
                        <div class="controls">
                       </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 3st row  -->

            
            <div class="row"> <!-- start 4st row  -->
                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <b> Date Of Birth :</b> {{ $emp->employee_date_of_birth}}</h5>
                        <div class="controls">
                       </div>
                        </div> 

                </div> <!-- end col md 4 -->

                <div class="col-md-4">

                    <div class="form-group">
                        <h5> <b>Employee Joing Date : </b>{{ $emp->employee_joing_date}}</h5>
                        <div class="controls">
                        </div>
                    </div> 
                    
                </div> <!-- end col md 4 -->
                
                
                <div class="col-md-4">
                    <div class="form-group">
                        <h5> <b>Employee Designation : </b>{{ $emp->designation}}</h5>
                        
                        <div class="controls">
                       </div>
                        </div>

                </div> <!-- end col md 4 -->

            </div> <!-- end 4st row  -->

            <div class="row"> <!-- start 5st row  -->
                <div class="col-md-6">
                    <div class="form-group" >
                        <div style="display: flex; flex-direction:column;">
                         <h5><b> Employee Present Address :</b></h5>
                              <p  id="employee_present_address" name="employee_present_address">
                                  {{$emp->employee_present_address}}
                              </p> 
                        </div>
                       
                         
                    </div> 

                </div> <!-- end col md 6 -->

                <div class="col-md-6">

                    <div class="form-group" >
                        <div style="display: flex; flex-direction:column;">
                         <h5><b> Employee Present Address :</b></h5>
                              <p  id="employee_present_address" name="employee_present_address" >{{$emp->employee_permanent_address}}</p> 
                        </div>
                         
                    </div> 

                </div> <!-- end col md 6 -->


            </div> <!-- end 5st row  -->


          <div class="text-xs-right">
            <input type="submit" hidden class="btn btn-rounded btn-primary mb-5" value="Update Employee">
                            </div>
                        </form>

                    </div>
                    <!-- /.col -->
                </div>
                <!-- /.row -->
                </div>
                <!-- /.box-body -->
            </div>
            <!-- /.box -->

            </section>
            <!-- /.content -->
        </div>


@endsection

 





