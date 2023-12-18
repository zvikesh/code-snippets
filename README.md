# Check: if the code is being called from Gateway

```

"Singleton SAP Gateway Consumption Class
  IF /iwfnd/cl_transaction_handler=>get_transaction_handler( )->get_service_name( ) EQ lc_sd_f2713_cep_srv. "SD_F2713_SOCUSTEXPDPRICE_SRV

    "Proceed with further checks if the routine, is called by Fiori App F2713
    "i.e.; SD_F2713_SOCUSTEXPDPRICE_SRV OData Service
    DATA(lt_request_params) = /iwfnd/cl_transaction_handler=>get_transaction_handler( )->get_all_request_params( ).

    TRY.
      " LT_REQUEST_PARAMS[]
      " NAME           VALUE
      " ~query_string  sap-client=200&SalesOrderID='0123456789'&ItemID='10'

     " LT_URI_FIELDS
     " NAME    VALUE
     " ItemID  '20'

      "Get Fields-Value paior from URI String
      DATA(lt_uri_fields)   = cl_http_utility=>string_to_fields( lt_request_params[ name = '~query_string' ]-value ). "No Binary Search, No of Records < 50

      "Get Item No from URI
      lv_kposn_odata        = |{ lt_uri_fields[ name = 'ItemID' ]-value ALPHA = IN }|.                                "No Binary Search, No of Records < 50

      IF komp-kposn EQ lv_kposn_odata          OR
         komp-cepok EQ lc_cep_status_released.    "C
        "If current item no is the one under processing by Fiori Application OR
        "If current Item is Expected Price Status is 'C' i.e.; Expected price not correct, but released
        "then skip his routine logic.
        DATA(lv_skip_sub_routine) = abap_true.
      ENDIF.

    CATCH cx_sy_itab_line_not_found INTO DATA(lo_itab_not_found).
      "Dummy variable to read text during debugging.
      "If itab line found, logic inside TRY-CATCH block should be skipped.
      DATA(lv_err_txt) = lo_itab_not_found->get_text( ).
    ENDTRY.
  ENDIF.

```
