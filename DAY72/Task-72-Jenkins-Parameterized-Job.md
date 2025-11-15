# 🌟 Task 72 - Create Parameterized Jenkins Job

**📌 Task Description**  
A new **DevOps Engineer** is joining the **Nautilus DevOps team** and needs to understand **parameterized builds** in Jenkins. The team wants to test a simple parameterized job with **string** and **choice** parameters to validate the basic functionality before assigning complex tasks.

**👉 Task Requirements**:  
- Access Jenkins UI and log in with **username**: `admin`, **password**: `Adm!n321`.  
- Create a **Freestyle project** job named `parameterized-job`.  
- Add a **String Parameter** named `Stage` with default value `Build`.  
- Add a **Choice Parameter** named `env` with choices: `Development`, `Staging`, `Production`.  
- Configure the job to execute a shell command that echoes both parameter values.  
- Build the job at least once with `env` set to `Staging` to verify functionality.

**💡 Note**: This task focuses on understanding parameterized builds, parameter types, and how to reference them in build steps.

---

## 🔹 Step 1: Access Jenkins UI

**Action**: Open Jenkins UI and log in with admin credentials.  
**Purpose**: Gain access to create and configure the parameterized job.

**Steps**:  
1. Click the **Jenkins** button in the top bar.  
2. Enter:  
   - **Username**: `admin`  
   - **Password**: `Adm!n321`  
3. Click **Log in**.

**Success Indicators**:  
- ✅ Login page loads successfully.  
- ✅ Jenkins dashboard appears after login.


---

## 🔹 Step 2: Create Jenkins Job `parameterized-job`

**Action**: Create a new Freestyle project job.  
**Purpose**: Set up the foundation for the parameterized build.

**Steps**:  
1. From the dashboard, click **New Item**.  
2. Enter:  
   - **Item name**: `parameterized-job`  
   - **Type**: Select **Freestyle project**  
3. Click **OK**.

**Success Indicators**:  
- ✅ Job configuration page opens.  
- ✅ Job name is `parameterized-job`.



---

## 🔹 Step 3: Enable Parameterized Build

**Action**: Enable parameterized build option for the job.  
**Purpose**: Allow the job to accept parameters during build execution.

**Steps**:  
1. In the job configuration page, locate the **General** section.  
2. Check the box for **This project is parameterized**.

**Success Indicators**:  
- ✅ Checkbox is selected.  
- ✅ Parameter addition options appear.



---

## 🔹 Step 4: Add String Parameter `Stage`

**Action**: Add a string parameter with a default value.  
**Purpose**: Allow dynamic input for the build stage.

**Steps**:  
1. Click **Add Parameter** dropdown.  
2. Select **String Parameter**.  
3. Configure the parameter:  
   - **Name**: `Stage`  
   - **Default Value**: `Build`  
   - **Description** (optional): `Build stage name`  
4. Leave other fields as default.

**Success Indicators**:  
- ✅ String parameter `Stage` is added.  
- ✅ Default value is set to `Build`.



---

## 🔹 Step 5: Add Choice Parameter `env`

**Action**: Add a choice parameter with predefined options.  
**Purpose**: Allow selection of deployment environment.

**Steps**:  
1. Click **Add Parameter** dropdown again.  
2. Select **Choice Parameter**.  
3. Configure the parameter:  
   - **Name**: `env`  
   - **Choices**: Enter each choice on a new line:  
```
     Development
     Staging
     Production
```  
   - **Description** (optional): `Target environment`  
4. Leave other fields as default.

**Success Indicators**:  
- ✅ Choice parameter `env` is added.  
- ✅ Three choices are listed: `Development`, `Staging`, `Production`.



---

## 🔹 Step 6: Add Shell Command Build Step

**Action**: Configure the job to execute a shell command that echoes parameter values.  
**Purpose**: Demonstrate how parameters are used within build steps.

**Steps**:  
1. Scroll down to the **Build** section.  
2. Click **Add build step** dropdown.  
3. Select **Execute shell**.  
4. In the **Command** text area, enter:  
```bash
   echo $Stage $env
```  
5. Click **Save** at the bottom of the page.

**Success Indicators**:  
- ✅ Execute shell build step is added.  
- ✅ Command correctly references both parameters: `$Stage` and `$env`.  
- ✅ Job configuration is saved successfully.



---

## 🔹 Step 7: Build the Job with Parameters

**Action**: Execute the job with `env` parameter set to `Staging`.  
**Purpose**: Verify that parameters work correctly and the job passes.

**Steps**:  
1. Navigate to the `parameterized-job` page (click job name from dashboard).  
2. Click **Build with Parameters** in the left sidebar.  
3. You will see both parameters:  
   - **Stage**: Leave as default `Build` (or modify if needed)  
   - **env**: Select `Staging` from the dropdown  
4. Click **Build**.  
5. Wait for the build to complete.

**Success Indicators**:  
- ✅ Build starts and appears in **Build History**.  
- ✅ Build status shows as **Success** (blue ball/checkmark).



---

## 🔹 Step 8: Verify Console Output

**Action**: Check the console output to confirm parameter values were echoed correctly.  
**Purpose**: Validate that the shell command executed with the correct parameter values.

**Steps**:  
1. In the **Build History**, click on the build number (e.g., `#1`).  
2. Click **Console Output** in the left sidebar.  
3. Review the output. You should see:  
```
   + echo Build Staging
   Build Staging
   Finished: SUCCESS
```

**Success Indicators**:  
- ✅ Console output shows `Build Staging` (or your chosen Stage value + Staging).  
- ✅ Build finishes with `Finished: SUCCESS`.


---

## 🔹 Step 9: Optional - Test with Different Parameter Values

**Action**: Run additional builds with different parameter combinations.  
**Purpose**: Demonstrate flexibility of parameterized builds.

**Steps**:  
1. Click **Build with Parameters** again.  
2. Try different combinations:  
   - **Stage**: `Deploy`, **env**: `Production`  
   - **Stage**: `Test`, **env**: `Development`  
3. Check console output for each build.

**Success Indicators**:  
- ✅ Each build completes successfully.  
- ✅ Console output reflects the parameter values used.



---




---

## 📋 Quick Reference Guide

**Jenkins UI Steps**:  
1. **Login**: `admin` / `Adm!n321`  
2. **Create Job**: New Item → `parameterized-job` → Freestyle project → OK  
3. **Enable Parameters**: General → Check "This project is parameterized"  
4. **Add String Parameter**:  
   - Name: `Stage`  
   - Default Value: `Build`  
5. **Add Choice Parameter**:  
   - Name: `env`  
   - Choices: `Development`, `Staging`, `Production` (one per line)  
6. **Add Build Step**: Execute shell  
```bash
   echo $Stage $env
```  
7. **Save** the job  
8. **Build with Parameters**:  
   - Stage: `Build` (default)  
   - env: `Staging`  
   - Click Build  
9. **Verify Console Output**: Should show `Build Staging`

---

## 💡 Common Issues & Fixes

### **Issue 1: Parameters Not Showing in Build**
**Problem**: "Build with Parameters" option not visible  
**Fix**:  
- Ensure "This project is parameterized" is checked  
- Save the job configuration  
- Refresh the job page  

### **Issue 2: Variables Not Expanding**
**Problem**: Output shows `$Stage $env` literally instead of values  
**Fix**:  
- Verify shell syntax: Use `$Stage` not `${Stage}` (both work, but `$` is standard)  
- Ensure no extra quotes around variables  
- Check for typos in parameter names  

### **Issue 3: Choices Not Appearing**
**Problem**: Choice parameter shows empty or single value  
**Fix**:  
- Ensure each choice is on a new line  
- No extra spaces before/after choice names  
- Save configuration after adding choices  

---

## 🔧 Troubleshooting Build Failures

### **Error: Build Fails to Start**
**Symptoms**: No build appears in history  
**Solution**:  
- Check Jenkins service status:  
```bash
ssh root@jenkins
sudo systemctl status jenkins
```  
- Review Jenkins logs:  
```bash
sudo journalctl -u jenkins -f
```

### **Error: Console Output Empty**
**Symptoms**: Build succeeds but no output visible  
**Solution**:  
- Verify execute shell command is saved  
- Try running a simple command first:  
```bash
echo "Test"
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:  
Understanding how to properly configure and reference different parameter types (String vs Choice) in Jenkins parameterized builds.

**💡 Solution Approach**:  
1. Enabled parameterized builds on the job  
2. Added String parameter with default value for flexibility  
3. Added Choice parameter with predefined options for controlled input  
4. Used shell variables (`$Stage`, `$env`) to reference parameters  
5. Validated with multiple test builds  

**🎯 Key Success Factors**:  
- Correct parameter naming (case-sensitive: `Stage` and `env`)  
- Proper choice formatting (one per line)  
- Correct variable syntax in shell command (`$PARAM_NAME`)  
- Testing with specified choice (`Staging`)  

**⚠️ Critical Configuration Points**:  
- "This project is parameterized" must be checked  
- Parameter names must match exactly in the shell command  
- Choices must be on separate lines for Choice parameter  
- At least one successful build with `Staging` required  

**🔒 Best Practices Applied**:  
- Clear parameter names that indicate purpose  
- Default values for String parameters  
- Logical choice ordering (Development → Staging → Production)  
- Simple validation via echo command  
- Console output verification  

**⚠️ Important Learning Concepts**:  
- **String Parameters**: Free-form text input with optional defaults  
- **Choice Parameters**: Dropdown selection from predefined values  
- **Variable Expansion**: Jenkins expands `$PARAM_NAME` during build  
- **Build History**: Each build preserves parameter values used  

---

## 🎓 Understanding Parameterized Builds

### **Why Use Parameterized Builds?**
- **Flexibility**: Single job for multiple scenarios  
- **Reusability**: Same job for different environments  
- **Control**: Predefined choices prevent errors  
- **Automation**: Can be triggered via API with parameters  

### **Parameter Types Available**:
1. **String Parameter**: Free text input  
2. **Choice Parameter**: Dropdown selection  
3. **Boolean Parameter**: Checkbox (true/false)  
4. **File Parameter**: Upload file during build  
5. **Password Parameter**: Masked input  

### **Referencing Parameters**:
- **In Shell Scripts**: `$PARAM_NAME` or `${PARAM_NAME}`  
- **In Windows Batch**: `%PARAM_NAME%`  
- **In Groovy**: `params.PARAM_NAME`  

---

## 🔄 Extending This Job

**Next Steps for Learning**:  
1. **Add Boolean Parameter**: Add a `DryRun` parameter  
2. **Conditional Logic**: Use if-else based on parameter values  
```bash
if [ "$env" == "Production" ]; then
  echo "Production deployment - extra validation needed"
else
  echo "Non-production deployment"
fi
```  
3. **Combine with Pipeline**: Convert to Jenkinsfile with parameters  
4. **API Triggering**: Trigger build via curl with parameters  

---

## ⚠️ Important Production Notes

🔧 **Validation**: Always validate parameter inputs in production jobs  
🔐 **Security**: Use Password parameters for sensitive data  
📊 **Documentation**: Add descriptions to all parameters  
🛡️ **Testing**: Test all parameter combinations before production use  
📹 **Audit**: Console output provides audit trail of parameter usage  

---

## ✅ Task Completion Checklist

- [ ] Logged into Jenkins with `admin`/`Adm!n321`  
- [ ] Created job named `parameterized-job`  
- [ ] Enabled "This project is parameterized"  
- [ ] Added String Parameter `Stage` with default `Build`  
- [ ] Added Choice Parameter `env` with three choices  
- [ ] Configured Execute shell build step with `echo $Stage $env`  
- [ ] Saved job configuration  
- [ ] Built job with `env` set to `Staging`  
- [ ] Verified console output shows parameter values  
- [ ] Documented all steps with screenshots  

**🎉 Success Criteria Met When**:  
- Job builds successfully with blue ball/checkmark  
- Console output displays: `Build Staging` (or chosen values)  
- "Build with Parameters" option is available  
- All parameters are configurable before each build