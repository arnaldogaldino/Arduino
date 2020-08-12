# Arduino

...or create a new repository on the command line
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/arnaldogaldino/Arduino.git
git push -u origin master

…or push an existing repository from the command line
git remote add origin https://github.com/arnaldogaldino/Arduino.git
git push -u origin master

﻿function XrmPage365(executionContext) {

    var XrmPage = executionContext;
    if (executionContext == undefined || executionContext == null) {
        XrmPage = Xrm.Page;
    }
    else if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }
    return XrmPage;
}

function RefreshForm(executionContext)
{
 var XrmPage = executionContext;
 var entity = {};
 entity["entityName"] = XrmPage.data.entity.getEntityName();
 entity["entityId"] = XrmPage.data.entity.getId();
 Xrm.Navigation.openForm(entity).then(
    function (success) {
        console.log(success);
    },
    function (error) {
        console.log(error);
    });
 
}
