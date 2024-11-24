# chembl2ctl

Repository with things to create CyTargetLinker files for ChEMBL. The linkset includes protein-target interactions with
a pChembl value above 6.

The output is disseminatied via Figshare and https://cytargetlinker.github.io/pages/linksets/chembl

## SPARQL queries

### Get information on the ineteractions

```SPARQL
PREFIX chembl: <http://rdf.ebi.ac.uk/terms/chembl#>
PREFIX cheminf: <http://semanticscience.org/resource/>

SELECT distinct ?target ?targetLabel ?molecule ?molLabel ?smiles ?inchi ?assayLabel ?type ?value ?pChembl WHERE {

  ?assay  chembl:hasTarget ?target.
  
  ?activity chembl:hasAssay  ?assay.
  ?activity chembl:hasMolecule ?molecule .

  ?target rdfs:label ?targetLabel.
  ?molecule rdfs:label ?molLabel.
  ?molecule cheminf:SIO_000008 [
    a cheminf:CHEMINF_000018;
	cheminf:SIO_000300 ?smiles
  ].
  ?molecule cheminf:SIO_000008 [
    a cheminf:CHEMINF_000113;
	cheminf:SIO_000300 ?inchi
  ].
  ?assay  rdfs:label ?assayLabel.
  
  ?activity chembl:type ?type.
  ?activity chembl:standardValue ?value.
  ?activity chembl:pChembl ?pChembl.
  FILTER (?pChembl > 6)

} limit 100
```

### Get info on the compounds

```SPARQL
PREFIX chembl: <http://rdf.ebi.ac.uk/terms/chembl#>
PREFIX cheminf: <http://semanticscience.org/resource/>
PREFIX chembl_mol: <http://rdf.ebi.ac.uk/resource/chembl/molecule/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX schema: <https://schema.org/>

Select ?identifier ?image ?smilesDepict where{
  
  ?s a chembl:SmallMolecule.
  ?s chembl:chemblId ?identifier.
  ?s foaf:depiction ?image.
  ?s cheminf:SIO_000008 [ a cheminf:CHEMINF_000018; 
							cheminf:SIO_000300 ?smilesDepict].
} LIMIT 100
```

### Get info on the protein targets

```SPARQL
SELECT * WHERE {
  <http://rdf.ebi.ac.uk/resource/chembl/target/CHEMBL1898>
    <http://rdf.ebi.ac.uk/terms/chembl#hasTargetComponent> ?tc .
  ?tc <http://www.w3.org/2004/02/skos/core#exactMatch> ?uniprotIRI ;
      <http://rdf.ebi.ac.uk/terms/chembl#organismName> ?organism ;
      <http://rdf.ebi.ac.uk/terms/chembl#componentType> ?type .
  FILTER (STRSTARTS(STR(?uniprotIRI), "http://purl.uniprot.org/uniprot/"))
}
```

## Example XGMML output

### Nodes

A protein:

```xml
   <node id="P35368" label="P35368">
      <att label="UniProt" name="UniProt" value="P35368" type="string"></att>
      <att label="HGNC" name="HGNC" value="ADRA1B" type="string"></att>
      <att type="list" name="identifiers">
         <att type="string" name="identifiers" value="P35368"></att>
         <att type="string" name="identifiers" value="147"></att>
         <att type="string" name="identifiers" value="ADRA1B"></att>
         <att type="string" name="identifiers" value="ENSG00000170214"></att>
      </att>
      <att label="biologicalType" name="biologicalType" value="protein" type="string"></att>
      <att label="label" name="label" value="ADRA1B" type="string"></att>
   </node>
```

Two compounds (with a few issues):

```xml
   <node id="CHEMBL2018866" label="CHEMBL2018866">
      <att type="list" name="identifiers">
         <att type="string" name="identifiers" value="CHEMBL2018866"></att>
      </att>
      <att label="biologicalType" name="biologicalType" value="compound" type="string"></att>
      <att type="list" name="identifiers">
         <att type="string" name="identifiers" value="Clc1ccc(cc1Cl)S(=O)(=O)N2CCc3ccccc3C2CC(=O)NC@@H4CCC@@H(CCC5=NCCN5)CC4"></att>
      </att>
      <att label="ChEMBL" name="ChEMBL" value="CHEMBL2018866" type="string"></att>
      <att label="label" name="label" value="CHEMBL2018866" type="string"></att>
   </node>
   <node id="CHEMBL257880" label="CHEMBL257880">
      <att type="list" name="identifiers">
         <att type="string" name="identifiers" value="CHEMBL257880"></att>
      </att>
      <att label="biologicalType" name="biologicalType" value="compound" type="string"></att>
      <att label="InCHI" name="InCHI" value="Cc1cn2cc(cc2c(n1)C#Cc3cccc(F)c3)C#N" type="string"></att>
      <att label="ChEMBL" name="ChEMBL" value="CHEMBL257880" type="string"></att>
      <att label="label" name="label" value="CHEMBL257880" type="string"></att>
   </node>
```

### Edge

```xml
   <edge id="173442" label="173442" source="CHEMBL225822" target="P16662">
      <att label="interaction" name="interaction" value="drug-target interaction" type="string">
      </att>
      <att label="datasource" name="datasource" value="ChEMBL (pChEMBL &gt; 6)_Homo sapiens_0.1" type="string"></att>
      <att label="interaction" name="interaction" value="drug-target interaction" type="string"></att>
      <att label="pChEMBL" name="pChEMBL" value="6.05" type="string"></att>
   </edge>
```

