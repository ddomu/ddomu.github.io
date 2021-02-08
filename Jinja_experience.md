--- 
{{ inventory_hostname }}
{% if dump_cmd in dwdm_show.result  %}
{% set lines =  dwdm_show.result[dump_cmd].split('\n') %}
{% set p = dict()  %}
{% for line in lines %}
{# {%- if (line | regex_search ('Executing .* [1-9]{1,2}\\.[1-9]{1,2}\\.[0-9]{1,2}') ) %}  
#}
{%- if ( 'Executing' in line ) %}
{% set _dummy = p.update( {'port' : line|regex_search ('[1-9]{1,2}\\.[1-9]{1,2}\\.[0-9]{1,2}')} )   %}
{% elif "Name" in line  %}
{% set _dummy = p.update( {'desc' :  line.split(':')[-1] }) %}
{%- elif "Err Cnt" in line  %}
{% set err = line.split(':') %}
   {{'_'.join(err[0].split()[-2:] )}}: {{err[1]}}
{%- if "Path BIP8" in line %}
    {{ p.port }} ({{ p.desc }})
{% endif %}
{%- endif -%}  
{%- endfor %}  
{% else %} {# Error to get result #}
ACCESS ERROR : {{ inventory_hostname }}
{% endif %}
{#  
#}
