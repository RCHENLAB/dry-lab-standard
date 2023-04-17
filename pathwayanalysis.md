

This code was generated using the selected tutorials:
* Introduction to PathfindR https://cran.r-project.org/web/packages/pathfindR/vignettes/intro_vignette.html
* Enrichment analysis using PathfindR https://egeulgen.github.io/pathfindR/
* pathfindR Analysis for non-Homo-sapiens organisms https://cran.r-project.org/web/packages/pathfindR/vignettes/non_hs_analysis.html

# Download and load the pathfindr package
# install.packages("pathfindR")
# To write a table with the significative p-values (File input - .csv with gene symbol, p-value and foldchange (this last one is optional)
# To generate wrapper to these functions

```
setwd("path/to/file")
```

# To human data, install the library org.Hs.eg.db first!

```
library(pathfindR)
library(KEGGgraph)
library(ggplot2)
```

# Arguments

```
pathwaysresults<-run_pathfindR(cornealimbus, 
                               gene_sets = "KEGG",
                               min_gset_size = 2,
                               max_gset_size = 300,
                               custom_genes = NULL,
                               custom_descriptions = NULL,
                               pin_name_path = "Biogrid",
                               p_val_threshold = 0.05,
                               visualize_enriched_terms = FALSE,
                               max_to_plot = NULL,
                               convert2alias = FALSE,
                               enrichment_threshold = 0.05,
                               adj_method = "bonferroni",
                               search_method = "GR",
                               plot_enrichment_chart = TRUE,
                               output_dir = "pathwayresultschooseanamefortheanalysisfolder",
                               list_active_snw_genes = TRUE,
                               silent_option = TRUE)
```

# To generate the graph with the enriched pathways - use description serves to display the pathway name on the network

```
cluster_enriched_terms(pathwaysresults, use_description = TRUE)
```

# To generate graphs for each enriched pathway:

```
input_results<-input_processing(
  cornealimbus,
  p_val_threshold = 0.05,
  pin_name_path = "Biogrid",
  convert2alias = TRUE
)

visualize_terms(pathwaysresults,
                input_processed = input_results,
                hsa_KEGG = FALSE,
                pin_name_path = "Biogrid")
```

# We need to try to fix the error about kegg maps https://github.com/egeulgen/pathfindR/issues/138

# The table must be contain 2 or 3 columns (gene name, p value, and or fold change)

# To save the pathway results table

```
write.csv(pathwaysresults, "filename_combinedkegg.csv")
```

# To generate a dotplot of enrichment chart

```
result<- enrichment_chart(pathwaysresults, top_terms = 20) #save as pdf the plot
results<- enrichment_chart(pathwaysresults, top_terms = 10)
results3<- enrichment_chart(pathwaysresults, top_terms = 5)
ggsave("result.pdf")
ggsave("result.png")
```

# Other functions

```
term_gene_graph(pathwaysresults, num_terms = 4,use_description = TRUE)
term_gene_heatmap(pathwaysresults, cornealimbus, num_terms = 4, use_description = TRUE)
```
