# studentForm

<!DOCTYPE html>
<!--
Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
Click nbfs://nbhost/SystemFileSystem/Templates/Other/html.html to edit this template
-->
<html>
    <head>
        <title>Student Form using JPDB</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="stylesheet"
              href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
        <script
        src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
        <script
        src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>
    </head>
    <body>
        <div class="container">
            <h2>Student Form using JPDB</h2>
            <form id="empForm" method="post">

                <div class="form-group">
                    <span><label>Roll No:</label> 
                        <input type="text" id="rollno" class="form-control" onchange="rollNo()">
                    </span>

                </div>
                <div class="form-group">
                    <label>Full Name:</label>
                    <input type="text" class="form-control" id="stdname">

                </div>
                <div class="form-group">
                    <label>Class:</label>
                    <input type="number" class="form-control" id="stdcls">

                </div>
                <div class="form-group">
                    <label>Birth Date:</label>
                    <input type="number" class="form-control" id="dob">

                </div>
                <div class="form-group">
                    <label>Address:</label>
                    <input type="text" class="form-control" id="add">

                </div>
                <div class="form-group">
                    <label>Enrollment Date:</label>
                    <input type="number" class="form-control" id="enroll">

                </div>
                <div class="form-group">
                    <button type="button" class="btn btn-primary" id="save" onclick="saveData()" disabled>Save</button>
                    <button type="button" class="btn btn-primary" id="change" onclick="changeData()" disabled>change</button>
                    <button type="button" class="btn btn-primary" id="reset" onclick="resetForm()" disabled>reset</button>
                </div>
            </form>

        </div>

        <script>

            var jpdbBaseURL = "http://api.login2explore.com:5577";
            var jpdbIRL = "/api/irl";
            var jpdbIML = "/api/iml";
            var empDbName = "std-DB";
            var empRelationName = "stdData";
            var connToken = "90932714|-31949276644004008|90954733";


            $("#rollNo").focus();

            function saveRecNo2LS(jsonObj) {
                var lvData = JSON.parse(jsonObj.data);
                localStorage.setItem("recno", lvData.rec_no);
            }
            function getStdIdAsJsonObj()
            {
                var rollno = $("#rollno").val();
                var jsonStr = {
                    id: rollno
                };
                return JSON.stringify(jsonStr);
            }

            function fillData(jsonObj) {
                saveRecNo2LS(jsonObj);
                var record = JSON.parse(jsonObj.data).record;
                $("#stdname").val("record.stdname");
                $("#stdcls").val("record.stdcls");
                $("#dob").val("record.dob");
                $("#add").val("record.add");
                $("#enroll").val("record.enroll");

            }
            function restForm() {

                $("#stdname").val("");
                $("#stdcls").val("");
                $("#dob").val("");
                $("#add").val("");
                $("#enroll").val("");
                $("#rollno").prop("disabled", false);
                $("#save").prop("disabled", true);
                $("#change").prop("disabled", true);
                $("#reset").prop("disabled", true);
                $("#rollno").focus();
            }

            function validateData()
            {
                var rollno, stdname, dob, add, enroll, stdcls;
                rollno = $("#rollno").val();
                stdname = $("#stdname").val();
                stdcls = $("#stdcls").val();
                dob = $("#dob").val();
                add = $("#add").val();
                enroll = $("#enroll").val();
                
                if (rollno === "")
                {
                    alert("Stundent Roll-No is missing");
                    $("#rollno").focus();
                    return "";
                }
                if (stdname === "")
                {
                    alert("Student name is missing");
                    $("#stdname").focus();
                    return "";
                }
                if (stdcls === "")
                {
                    alert("Student class is missing");
                    $("#stdcls").focus();
                    return "";
                }
                if (dob === "")
                {
                    alert("Student Date of Birth is missing");
                    $("#dob").focus();
                    return "";
                }
                if (add === "")
                {
                    alert("Student address is missing");
                    $("#add").focus();
                    return "";
                }
                if (enroll === "")
                {
                    alert("Student Enrollment Number is missing");
                    $("#enroll").focus();
                    return "";
                }

                var jsonStrObj = {
                    rollno: rollno,
                    stdname: stdname,
                    stdcls: stdcls,
                    add: add,
                    dob: dob,
                    enroll: enroll

                };
                return JSON.stringify(jsonStrObj);
            }
            function getStd() {
                var empIdJsonObj = getEmpIdAsJsonObj();
                var getRequest = creatGet_BY_KEYReqest(connToken, stdDbName, stdRelationName, stdIdJsonObj);

                jQuery.ajaxSetup({async: false});
                var resJsonObj = executeCommandAtGivenBaseUrl(getRequest, jpdbBaseURL, jpdbIRL);
                jQuery.ajaxSetup({async: true});

                if (resJsonObj.status === 400) {
                    $("#save").prop("disabled", false);
                    $("#reset").prop("disabled", false);
                    $("#stdname").focus();
                } else if (resJsonObj.status === 200)
                {
                    $("#rollno").prop("disabled", true);
                    fillData(resJsonObj);
                    $("#change").prop("disabled", false);
                    $("#reset").prop("disabled", false);
                    $("#stdname").focus();
                }
            }

            function saveData() {
                var jsonStrObj = validateData();
                if (jsonStrObj === "")
                {
                    return"";
                }

                var putRequest = createPutRequest(connToken, jsonStrObj, empDbName, empRelationName);
                jQuery.ajaxSetup({async: false});
                var resJsonObj = executeCommandAtGivenBaseUrl(putRequest, jpdbBaseURL, jpdbIML);
                jQuery.ajaxSetup({async: true});
                resteForm();
                $("#rollno").focus();

            }

            function changeDate()
            {
                $("#change").prop("disabled", true);
                jsonChg = validateData();
                var updateRequest = createUPDATERecordRequest(connToken, jsonChg, empDbName, empRelationName, localStorage.getItem("recno"));
                jQuery.ajaxSetup({async: false});
                var resJsonObj = executeCommandAtGivenBaseUrl(updateRequest, jpdbBaseURL, jpdbIML);
                jQuery.ajaxSetup({async: true});
                console.log(resJsonObj);
                resteForm();
                $("#rollno").focus();

            }


        </script>
    </body>
</html>
