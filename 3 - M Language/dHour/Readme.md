### Description [en-us]

Using M language (in Power Query) to automate creating hours according to the data source.

### Code in M language

```powershell

let
    Lista = {0..86399},
    ConverteParaTabela = Table.FromList(Lista, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    AlteraTipo = Table.TransformColumnTypes(ConverteParaTabela,{{"Column1", Int64.Type}}),
    AlteraNomeCol = Table.RenameColumns(AlteraTipo,{{"Column1", "Segundo"}}),
    ColDuracao = Table.AddColumn(AlteraNomeCol,"Duração", each #duration(0,0,0,[Segundo]), type duration),
    ColHora = Table.AddColumn(ColDuracao, "Hora", each #time(Duration.Hours([Duração]),Duration.Minutes([Duração]),Duration.Seconds([Duração])), type time),
    RemoveCols = Table.RemoveColumns(ColHora,{"Duração", "Segundo"}),
    ColHoraNum = Table.AddColumn(RemoveCols, "Hora Num", each Time.Hour([Hora]), Int64.Type),
    ColMinutoNum = Table.AddColumn(ColHoraNum, "Minuto Num", each Time.Minute([Hora]), Int64.Type),
    ColSegundoNum = Table.AddColumn(ColMinutoNum, "Segundo Num", each Time.Second([Hora]), Int64.Type),
    ColAMPM = Table.AddColumn(ColSegundoNum,"AMPM", each if [Hora Num] < 12 then "AM" else "PM", type text),
    ColHoraAMPM = Table.AddColumn(ColAMPM, "Hora AMPM", each 
        if [Hora Num] = 0 then "12 AM"
        else if [Hora Num] >= 1 and [Hora Num] <= 11 then Number.ToText([Hora Num]) & " AM" 
        else if [Hora Num] = 12 then "12 PM"
        else if [Hora Num] >= 13 then Number.ToText([Hora Num] - 12) & " PM"  
        else "Unknown", type text),
    ColPeriodo = 
        Table.AddColumn(ColHoraAMPM, "Período", each 
            if        [Hora Num] >= 0 and [Hora Num] <= 5 then "12AM to 6AM"
            else if   [Hora Num] >= 6 and [Hora Num] <= 11 then "6AM to 12PM"
            else if   [Hora Num] >= 12 and [Hora Num] <= 17  then "12PM to 6PM"
            else if   [Hora Num] >= 18 and [Hora Num] <= 23 then "6PM to 12AM"
            else "Unknown", type text
            ),
    ColPeriodoOrdem = 
        Table.AddColumn(ColPeriodo, "Período Ordem", each 
            if        [Hora Num] >= 0 and [Hora Num] <= 5 then 1
            else if   [Hora Num] >= 6 and [Hora Num] <= 11 then 2
            else if   [Hora Num] >= 12 and [Hora Num] <= 17  then 3
            else if   [Hora Num] >= 18 and [Hora Num] <= 23 then 4
            else 5, Int64.Type
        )
in
    ColPeriodoOrdem

```

##

### Descrição [pt-br]

Usando a linguagem M (no Power Query) para automatizar a criação de horas de acordo com a fonte de dados.  

### Código na linguagem M

```powershell

let
    Lista = {0..86399},
    ConverteParaTabela = Table.FromList(Lista, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    AlteraTipo = Table.TransformColumnTypes(ConverteParaTabela,{{"Column1", Int64.Type}}),
    AlteraNomeCol = Table.RenameColumns(AlteraTipo,{{"Column1", "Segundo"}}),
    ColDuracao = Table.AddColumn(AlteraNomeCol,"Duração", each #duration(0,0,0,[Segundo]), type duration),
    ColHora = Table.AddColumn(ColDuracao, "Hora", each #time(Duration.Hours([Duração]),Duration.Minutes([Duração]),Duration.Seconds([Duração])), type time),
    RemoveCols = Table.RemoveColumns(ColHora,{"Duração", "Segundo"}),
    ColHoraNum = Table.AddColumn(RemoveCols, "Hora Num", each Time.Hour([Hora]), Int64.Type),
    ColMinutoNum = Table.AddColumn(ColHoraNum, "Minuto Num", each Time.Minute([Hora]), Int64.Type),
    ColSegundoNum = Table.AddColumn(ColMinutoNum, "Segundo Num", each Time.Second([Hora]), Int64.Type),
    ColAMPM = Table.AddColumn(ColSegundoNum,"AMPM", each if [Hora Num] < 12 then "AM" else "PM", type text),
    ColHoraAMPM = Table.AddColumn(ColAMPM, "Hora AMPM", each 
        if [Hora Num] = 0 then "12 AM"
        else if [Hora Num] >= 1 and [Hora Num] <= 11 then Number.ToText([Hora Num]) & " AM" 
        else if [Hora Num] = 12 then "12 PM"
        else if [Hora Num] >= 13 then Number.ToText([Hora Num] - 12) & " PM"  
        else "Unknown", type text),
    ColPeriodo = 
        Table.AddColumn(ColHoraAMPM, "Período", each 
            if        [Hora Num] >= 0 and [Hora Num] <= 5 then "12AM to 6AM"
            else if   [Hora Num] >= 6 and [Hora Num] <= 11 then "6AM to 12PM"
            else if   [Hora Num] >= 12 and [Hora Num] <= 17  then "12PM to 6PM"
            else if   [Hora Num] >= 18 and [Hora Num] <= 23 then "6PM to 12AM"
            else "Unknown", type text
            ),
    ColPeriodoOrdem = 
        Table.AddColumn(ColPeriodo, "Período Ordem", each 
            if        [Hora Num] >= 0 and [Hora Num] <= 5 then 1
            else if   [Hora Num] >= 6 and [Hora Num] <= 11 then 2
            else if   [Hora Num] >= 12 and [Hora Num] <= 17  then 3
            else if   [Hora Num] >= 18 and [Hora Num] <= 23 then 4
            else 5, Int64.Type
        )
in
    ColPeriodoOrdem

```