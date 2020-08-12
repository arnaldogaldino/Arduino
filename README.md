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



function LoadDuplaAprovacao(executionContext, buttonElementId) {

    console.log("LoadDuplaAprovacao")
    var XrmPage = XrmPage365(executionContext);
    OcultaCamposRascunho(executionContext, OcultaCamposRascunho);
    hideButtonEditar(executionContext, buttonElementId);
    validaEstadoCampos(executionContext);
}

function MostraCamposRascunho(executionContext) {
    debugger;
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }

    //Associar ao command do botão editar
    if (!ContatoExecutvo(executionContext)) {
        ToogleCamposDuplaAprovacao(executionContext, true);
        var statusAprovacao = XrmPage.data.entity.attributes.get('bz_statuscip');

        if (statusAprovacao == null) {
            statusAprovacao = XrmPage.data.entity.attributes.get('cip_status_aprovacao');
        }
        statusAprovacao.setValue(1);
    }
    else {
        desbloquearCampoosContatoExecutivo(executionContext);
        //retira obrigatoriedade dos campos rascunho
        XrmPage.getAttribute("bz_classificaodocontato").setRequiredLevel("none");
        XrmPage.getAttribute("bz_nacionalidadedocontatorascunho").setRequiredLevel("none");
        XrmPage.getAttribute("bz_nomerascunho").setRequiredLevel("none");
        XrmPage.getAttribute("bz_ltimonomerascunho").setRequiredLevel("none");
        // XrmPage.getAttribute("bz_empresarascunho").setRequiredLevel("none");
        XrmPage.getAttribute("bz_telefonecomercialrascunho").setRequiredLevel("none");
        XrmPage.getAttribute("bz_emailcomercialrascunhomasc").setRequiredLevel("none");
        XrmPage.getAttribute("bz_rearascunho").setRequiredLevel("none");
        XrmPage.getAttribute("bz_cargorascunhon").setRequiredLevel("none");
    }
}

function ContatoExecutvo(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }

    var retorno = false;
    var entity = XrmPage.data.entity.getEntityName();

    if (entity == "contact") {
        if (XrmPage.getAttribute("customertypecode").getSelectedOption()) {
            var contato_executivo = XrmPage.getAttribute("customertypecode").getSelectedOption().value;

            if (contato_executivo == 1 || contato_executivo == 3) {
                retorno = true;
            }
        }
    }
    else {
        retorno = false;
    }

    return retorno;
}

function desbloquearCampoosContatoExecutivo(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }
    var campos = ["customertypecode", "bz_nacionalidadedocontato", "bz_cotista", "firstname", "lastname", "telephone1", "bz_ramal", "mobilephone", "telephone2", "business2", "emailaddress1", "emailaddress2", "emailaddress3", "bz_secretaria", "bz_area", "bz_cargo", "jobtitle", "bz_endereonacional", "address1_addresstypecode", "address1_postalcode", "bz_tipodelogradouro", "address1_line1", "bz_numero", "address1_line2", "address1_line3", "address1_city", "address1_stateorprovince", "address1_country", "bz_enderecointernacional", "gendercode", "birthdate", "bz_tipodedocumento", "bz_nmerododocumento", "bz_cpf", "bz_preferencias"];
    for (var i = 0; i < campos.length; i++) {
        for (var i = 0; i < campos.length; i++) {
            XrmPage.ui.controls.get(campos[i]).setDisabled(false);
        }

    }
}

function OcultaCamposRascunho(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    ToogleCamposDuplaAprovacao(executionContext, false);
}

function hideButtonEditar(executionContext, buttonElementId) {
    var XrmPage = XrmPage365(executionContext);

    var intervalId = window.setInterval(function () {

        var statusAprovacao = XrmPage.data.entity.attributes.get('bz_statuscip');

        if (statusAprovacao == null) {
            statusAprovacao = XrmPage.data.entity.attributes.get('cip_status_aprovacao');
        }

        //1 Em Criação
        //2 Em Aprovação
        //3 Aprovado
        //alert(statusAprovacao.getValue());
        if (statusAprovacao.getValue() == 3) //Se diferente de Aprovado, esconde o campo Editar
        {
            var btnEditar = top.document.getElementById(buttonElementId);
            if (btnEditar != null) {
                btnEditar.style.display = 'inline-block';

            }
        }
        else {
            //Verifica se não é o propietario do registro para não editar 2 vezes.
            var btnEditar = top.document.getElementById(buttonElementId);
            if (btnEditar != null) {
                btnEditar.style.display = 'none';

            }
        }
    },
        100);
}

function validaEstadoCampos(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    //Verifica se o registro está com o status em Aprovação.

    var intervalId = window.setInterval(function () {

        var statusAprovacao = XrmPage.data.entity.attributes.get('bz_statuscip');

        if (statusAprovacao == null) {
            statusAprovacao = XrmPage.data.entity.attributes.get('cip_status_aprovacao');
        }

        //1 Em Criação
        //2 Em Aprovação
        //3 Aprovado
        //alert(statusAprovacao.getValue());
        if (statusAprovacao.getValue() == 1) //Se diferente de  Em Criação, esconde os campos de edição
        {
            ToogleCamposDuplaAprovacao(executionContext, true);
        }
        else {
            ToogleCamposDuplaAprovacao(executionContext, false);
        }
    },
        1000);
}

function ToogleCamposDuplaAprovacao(executionContext, rascunho) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }

    var oDataSelect = Xrm.Utility.getGlobalContext().getClientUrl() + "/xrmservices/2011/OrganizationData.svc/bz_mapeamentoduplaaprovacaoSet?$select=bz_Entidade,bz_MapeamentoDe,bz_MapeamentoPara&$filter=bz_Entidade eq '" + XrmPage.data.entity.getEntityName() + "'";
    $.ajax({
        type: "GET",
        async: false,
        contentType: "application/json; charset=utf-8",
        datatype: "json",
        url: oDataSelect,
        beforeSend: function (XMLHttpRequest) {
            XMLHttpRequest.setRequestHeader("Accept", "application/json");
        },
        success: function (data, textStatus, XmlHttpRequest) {
            try {
                if (data.d.results != null) {
                    var x = data.d.results[0];

                    var fieldsListDe = [];
                    var fieldsListPara = [];

                    for (var i = 0; i < data.d.results.length; i++) {
                        var item = data.d.results[i];

                        fieldsListPara.push(item.bz_MapeamentoPara);
                        fieldsListDe.push(item.bz_MapeamentoDe);

                    }

                    var statusAprovacao = XrmPage.data.entity.attributes.get('bz_statuscip');

                    if (statusAprovacao == null) {
                        statusAprovacao = XrmPage.data.entity.attributes.get('cip_status_aprovacao');
                    }

                    //1 Em Criação
                    //2 Em Aprovação
                    //3 Aprovado
                    if (rascunho == false && statusAprovacao.getValue() != 1) //Desabilita Rascunhos e Habilitas Oficiais
                    {
                        _rascunho = false;
                        show(executionContext, fieldsListDe);
                        hide(executionContext, fieldsListPara);
                        buscaCampoEmpresa(executionContext);
                        //mostraMiniCv(false);
                    }
                    else //Habilita Rascunhos e Habilitas Oficiais
                    {
                        _rascunho = true;
                        hide(executionContext, fieldsListDe);
                        show(executionContext, fieldsListPara);
                        buscaCampoEmpresaRasc(executionContext);
                        //mostraMiniCv(true);
                    }

                }
            } catch (e) {
                //alert(e.description);
            }
        },
        error: function (XmlHttpRequest, textStatus, errorThrown) {
            alert('Erro na chamada: ' + errorThrown);
        }
    });
}

function show(executionContext, fieldsList) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }
    for (var i = 0; i < fieldsList.length; i++) {
        try {
            var fieldName = fieldsList[i].toLowerCase();
            if (exeptionFields(executionContext, fieldName)) {
                console.log("Campos show exception: " + fieldName);
            }
            else {
                console.log("Campos Show : " + fieldName);
                setShowField(executionContext, fieldName.toLowerCase(), true);
            }
        }
        catch (e) {
            console.log("Erro na funcao customizada show. Erro: " + e.message + "Campo: " + fieldsList[i]);
        }
    }
}

function hide(executionContext, fieldsList) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }
    for (var i = 0; i < fieldsList.length; i++) {
        try {
            var fieldName = fieldsList[i].toLowerCase();
            if (exeptionFields(executionContext, fieldName)) {
                console.log("Campos Hide exception: " + fieldName);
            }
            else {
                console.log("Campos hide : " + fieldName);
                setShowField(executionContext, fieldName.toLowerCase(), false);
            }
        }
        catch (e) {
            console.log("Erro na funcao customizada hide. Erro: " + e.message + "Campo: " + fieldsList[i]);
        }
    }
}

function setShowField(executionContext, fieldName, show) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }
    if (XrmPage.ui.controls.get(fieldName) != null) XrmPage.ui.controls.get(fieldName).setVisible(show);
}

var _rascunho = false;

function exeptionFields(executionContext, field) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }
    var retorno = false;
    switch (field.toLowerCase()) {
        case 'bz_cpfrascunho':
            if (_rascunho) retorno = ShowHideAddressFieldsContato(executionContext, field, true, true);
            else setShowField(executionContext, field, false);
            break;
        case 'bz_ceprascunho':
        case 'bz_logradourorascunho':
        case 'bz_nmerorascunho':
        case 'bz_complementorascunho':
        case 'bz_bairrorascunho':
        case 'bz_cidaderascunho':
        case 'bz_estadorascunho':
        case 'bz_endereointernacionalrascunho':
        case 'bz_tipodelogradourorascunho':
            if (_rascunho) retorno = ShowHideAddressFieldsContato(executionContext, field, true, false);
            else setShowField(executionContext, field, false);
            break;
        case 'bz_cpf':
            if (!_rascunho) retorno = ShowHideAddressFieldsContato(executionContext, field, false, true);
            else setShowField(executionContext, field, false);
            break;
        case 'address1_postalcode':
        case 'address1_line1':
        case 'bz_numero':
        case 'address1_line2':
        case 'address1_line3':
        case 'address1_city':
        case 'address1_stateorprovince':
        case 'bz_enderecointernacional':
        case 'bz_tipodelogradouro':
            if (!_rascunho) retorno = ShowHideAddressFieldsContato(executionContext, field, false, false);
            else setShowField(executionContext, field, false);
            break;
        //case 'bz_tipodelogradourorascunho':
        //case 'bz_endereointernacionalrascunho':
        //    if (_rascunho)
        //        setShowField(field, true);
        //    else
        //        setShowField(field, false);
        //    break;
        //case 'bz_enderecointernacional':
        //case 'bz_tipodelogradouro':
        //    if (!_rascunho)
        //        setShowField(field, true);
        //    else
        //        setShowField(field, false);
        //    break;
        case "bz_cotista":
            break;
        case "bz_representantelegaldocotistarascunho":
            break;
        case 'bz_minicvrascunho':
        case 'bz_minicv':
            //mostraMiniCv(_rascunho);
            return true;
            break;
        default:
    }
    return retorno;
}

function ShowHideAddressFieldsContato(executionContext, _campo, _rascunho, required) {
    var XrmPage = XrmPage365(executionContext);

    if (_rascunho) {
        var endereco_nacional = XrmPage.getAttribute("bz_endereonacionalrascunho");
        if (endereco_nacional != null && endereco_nacional.getValue() != null) {
            // NÂO = 0 / SIM = 1
            if (endereco_nacional.getValue() == 0) {

                if (_campo == "bz_endereointernacionalrascunho") {
                    XrmPage.ui.controls.get(_campo).setVisible(true);
                    if (required) XrmPage.getAttribute(_campo).setRequiredLevel("required");
                }
                else if (_campo == "bz_cpfrascunho") {
                    XrmPage.ui.controls.get(_campo).setVisible(true);
                    if (required) XrmPage.getAttribute(_campo).setRequiredLevel("none");
                }
                else {
                    XrmPage.ui.controls.get(_campo).setVisible(false);
                    XrmPage.getAttribute(_campo).setRequiredLevel("none");
                }
            }
            else if (endereco_nacional.getValue() == 1) {
                if (_campo == "bz_endereointernacionalrascunho") {
                    XrmPage.ui.controls.get(_campo).setVisible(false);
                    XrmPage.getAttribute(_campo).setRequiredLevel("none");
                }
                else {
                    if (_campo == "bz_cpfrascunho") {
                        setRequiredCPF(executionContext, _rascunho);
                    } else {
                        XrmPage.ui.controls.get(_campo).setVisible(true);
                        if (required) XrmPage.getAttribute(_campo).setRequiredLevel("required");
                    }
                }
            }
        }
        else {
            if (_campo == "bz_endereointernacionalrascunho") {
                XrmPage.ui.controls.get(_campo).setVisible(false);
                XrmPage.getAttribute(_campo).setRequiredLevel("none");
            }
            else {
                if (_campo == "bz_cpfrascunho") {
                    setRequiredCPF(executionContext, _rascunho);
                } else {
                    XrmPage.ui.controls.get(_campo).setVisible(true);
                    if (required) XrmPage.getAttribute(_campo).setRequiredLevel("required");
                }
            }
        }

        retorno = true;
    }
    else {
        var endereco_nacional = XrmPage.getAttribute("bz_endereonacional");
        if (endereco_nacional != null && endereco_nacional.getValue() != null) {
            // NÂO = 0 / SIM = 1
            if (endereco_nacional.getValue() == 0) {

                if (_campo == "bz_enderecointernacional") {
                    XrmPage.ui.controls.get(_campo).setVisible(true);
                    XrmPage.getAttribute(_campo).setRequiredLevel("required");
                }
                else if (_campo == "bz_cpf") {
                    XrmPage.ui.controls.get(_campo).setVisible(true);
                    XrmPage.getAttribute(_campo).setRequiredLevel("none");
                }
                else {
                    XrmPage.ui.controls.get(_campo).setVisible(false);
                    XrmPage.getAttribute(_campo).setRequiredLevel("none");
                }
            }
            else if (endereco_nacional.getValue() == 1) {
                if (_campo == "bz_enderecointernacional") {
                    XrmPage.ui.controls.get(_campo).setVisible(false);
                    XrmPage.getAttribute(_campo).setRequiredLevel("none");
                }
                else {
                    if (_campo == "bz_cpf") {
                        setRequiredCPF(executionContext, _rascunho);
                    } else {
                        XrmPage.ui.controls.get(_campo).setVisible(true);
                        if (required) XrmPage.getAttribute(_campo).setRequiredLevel("required");
                    }
                }
            }
        }
        retorno = true;
    }
    return retorno;
}

function VerificaCampoPreenchidoForm(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    var atributos = XrmPage.data.entity.attributes.get();
    var campos = [];
    var cont = 0;
    for (var i in atributos) {
        var name = atributos[i].getName();
        var preenchido = XrmPage.getAttribute(name).getIsDirty();
        if (preenchido) {
            campos[0] = name;
            cont++;
        }
    }

    return campos;
}

function onSaveConfigSistema(executionContext, buttonElementId) {
    debugger;
    var XrmPage = XrmPage365(executionContext);
    var entityName = XrmPage.data.entity.getEntityName();

    if (entityName == "bz_configuracaosistemawebcipprod" || entityName == "bz_configuracaosistemawebcip") {
        var gravar = false;
        var campos = VerificaCampoPreenchidoForm(executionContext);

        if (campos.length > 0) {
            for (var i = 0, len = campos.length; i < len; i++) {
                if (campos[i] == "bz_nomedraft" || campos[i] == "bz_descricaodraft" || campos[i] == "bz_tipodedadodraft" || campos[i] == "bz_conteudodraft") {
                    gravar = true;
                    break;
                }
            }
        }

        if (!gravar) {
            XrmPage.getAttribute("bz_statuscip").setSubmitMode("never");
        }
        else {
            LoadDuplaAprovacao(executionContext, buttonElementId);
        }
    }
}

function setRequiredCPF(executionContext, _rascunho) {
    var XrmPage = XrmPage365(executionContext);
    if (_rascunho) {
        var classificacaoRascunho = XrmPage.getAttribute("bz_classificaodocontato").getSelectedOption();
        if (classificacaoRascunho != null) {
            if (classificacaoRascunho.value == 1 || classificacaoRascunho.value == 2) {
                XrmPage.ui.controls.get("bz_cpfrascunho").setVisible(true);
                XrmPage.getAttribute("bz_cpfrascunho").setRequiredLevel("none");
            } else {
                XrmPage.ui.controls.get("bz_cpfrascunho").setVisible(true);
                XrmPage.getAttribute("bz_cpfrascunho").setRequiredLevel("none");
            }
        } else {
            XrmPage.ui.controls.get("bz_cpfrascunho").setVisible(true);
            XrmPage.getAttribute("bz_cpfrascunho").setRequiredLevel("none");
        }
    } else {
        var classificacao = XrmPage.getAttribute("customertypecode").getSelectedOption();
        if (classificacao != null) {
            if (classificacao.value == 1 || classificacao.value == 2) {
                XrmPage.ui.controls.get("bz_cpf").setVisible(true);
                XrmPage.getAttribute("bz_cpf").setRequiredLevel("none");
            } else {
                XrmPage.ui.controls.get("bz_cpf").setVisible(true);
                XrmPage.getAttribute("bz_cpf").setRequiredLevel("none");
            }
        } else {
            XrmPage.ui.controls.get("bz_cpf").setVisible(true);
            XrmPage.getAttribute("bz_cpf").setRequiredLevel("none");
        }
    }
}

function buscaCampoEmpresa(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    if (executionContext.getFormContext != undefined && executionContext.getFormContext != null) {
        XrmPage = executionContext.getFormContext();
    }
    if (XrmPage.data.entity.getEntityName() == "contact") {
        var empresa = {};
        var req = new XMLHttpRequest();
        req.open("GET", Xrm.Utility.getGlobalContext().getClientUrl() + "/api/data/v8.1/cip_empresasdocontatos?$select=cip_name&$expand=cip_empresa($select=bz_empresacotistacip)&$filter=_cip_contato_value eq " + XrmPage.data.entity.getId().replace("{", "").replace("}", ""), false);
        req.setRequestHeader("OData-MaxVersion", "4.0");
        req.setRequestHeader("OData-Version", "4.0");
        req.setRequestHeader("Accept", "application/json");
        req.setRequestHeader("Content-Type", "application/json; charset=utf-8");
        req.setRequestHeader("Prefer", "odata.include-annotations=\"*\"");
        req.onreadystatechange = function () {
            if (this.readyState === 4) {
                req.onreadystatechange = null;
                if (this.status === 200) {
                    var results = JSON.parse(this.response);
                    for (var i = 0; i < results.value.length; i++) {
                        empresa.name = results.value[i]["cip_name"];
                        //Use @odata.nextLink to query resulting related records
                        empresa.empresa = results.value[i].cip_empresa.bz_empresacotistacip;
                    }
                } else {

                }
            }
        };
        req.send();

        var count = 0;
        for (var i = 0; i < results.value.length; i++) {
            if (empresa.empresa == 1) {
                count++;
            }
        }

        XrmPage.ui.controls.get("bz_cotista").setVisible(count > 0);
    }
}

function buscaCampoEmpresaRasc(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    if (XrmPage.data.entity.getEntityName() == "contact") {
        var url = Xrm.Utility.getGlobalContext().getClientUrl() + "/api/data/v8.1/cip_empresasdocontatos?$select=cip_name&$expand=cip_empresa($select=bz_empresacotistacip)&$filter=_cip_contato_value eq " + XrmPage.data.entity.getId().replace("{", "").replace("}", "")
        var empresa = {};
        var results;
        var req = new XMLHttpRequest();
        req.open("GET", url, false);
        req.setRequestHeader("OData-MaxVersion", "4.0");
        req.setRequestHeader("OData-Version", "4.0");
        req.setRequestHeader("Accept", "application/json");
        req.setRequestHeader("Content-Type", "application/json; charset=utf-8");
        req.setRequestHeader("Prefer", "odata.include-annotations=\"*\"");
        req.onreadystatechange = function () {
            if (this.readyState === 4) {
                req.onreadystatechange = null;
                if (this.status === 200) {
                    results = JSON.parse(this.response);
                    for (var i = 0; i < results.value.length; i++) {
                        empresa.name = results.value[i]["cip_name"];
                        //Use @odata.nextLink to query resulting related records
                        empresa.empresa = results.value[i].cip_empresa.bz_empresacotistacip;
                    }
                } else {

                }
            }
        };
        req.send();

        var count = 0;
        if (results != null) {
            for (var i = 0; i < results.value.length; i++) {
                if (empresa.empresa == 1) {
                    count++;
                }
            }
        }

        if (count > 0) {
            XrmPage.ui.controls.get("bz_representantelegaldocotistarascunho").setVisible(true);
        } else {
            XrmPage.ui.controls.get("bz_representantelegaldocotistarascunho").setVisible(false);
        }
        //}
    }
}

function mostraMiniCv(executionContext, _rascunho) {
    var nomefuncao = "mostraMiniCv";
    var XrmPage = XrmPage365(executionContext);
    var representanteRascunho = XrmPage.getAttribute("bz_representantelegaldocotistarascunho").getValue();
    var representante = XrmPage.getAttribute("bz_cotista").getValue();

    setShowField(executionContext, "bz_minicvrascunho", false);
    setShowField(executionContext, "bz_minicv", false);

    if (_rascunho) {
        if (representanteRascunho != null) {
            if (representanteRascunho == 1) setShowField(executionContext, "bz_minicvrascunho", true);
        }
    } else {
        if (representante != null) {
            if (representante == 1) setShowField(executionContext, "bz_minicv", true);
        }
    }
}

function contato_assessoria(executionContext) {
    var XrmPage = XrmPage365(executionContext);
    if (XrmPage.getAttribute("bz_classificaodocontato").getSelectedOption()) {
        if (XrmPage.getAttribute("bz_classificaodocontato").getSelectedOption().value == 1) {
            XrmPage.getAttribute("bz_telefonecomercialrascunho").setRequiredLevel("none");
        } else {
            XrmPage.getAttribute("bz_telefonecomercialrascunho").setRequiredLevel("required");
        }
    }
}
