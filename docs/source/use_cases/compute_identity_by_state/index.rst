Compute Identity By State
==========================

.. comment: begin: goto-read-the-docs

.. container:: visible-only-on-github

   +-----------------------------------------------------------------------------------+
   | **The properly rendered version of this document can be found at Read The Docs.** |
   |                                                                                   |
   | **If you are reading this on github, you should instead click** `here`__.         |
   +-----------------------------------------------------------------------------------+

.. _RenderedVersion: http://googlegenomics.readthedocs.org/en/latest/use_cases/compute_identity_by_state/index.html

__ RenderedVersion_

.. comment: end: goto-read-the-docs

.. toctree::
   :maxdepth: 2

.. contents::

`Identity-by-State <https://www.youtube.com/watch?v=NRiI1RbE_-I>`_ is a simple similarity measure that describes the alleles shared by two individuals as a single number.

See the `Quality Control using Google Genomics codelab <https://github.com/googlegenomics/codelabs/blob/master/R/PlatinumGenomes-QC/Sample-Level-QC.md#genome-similarity>`_ for an example that makes use of the results of this analysis run upon :doc:`/use_cases/discover_public_data/platinum_genomes`.

A `Google Cloud Dataflow`_ implementation is available.

Setup Dataflow
--------------

.. include:: /includes/collapsible_dataflow_setup_instructions.rst

Run the pipeline
----------------
The following command will run Identity-by-State over the BRCA1 region within the :doc:`/use_cases/discover_public_data/platinum_genomes` dataset.

.. code-block:: shell

  java -cp /PATH/TO/google-genomics-dataflow*runnable.jar \
    com.google.cloud.genomics.dataflow.pipelines.IdentityByState \
    --project=YOUR-GOOGLE-CLOUD-PLATFORM-PROJECT-ID \
    --stagingLocation=gs://YOUR-BUCKET/dataflow-staging \
    --secretsFile=/PATH/TO/YOUR/client_secrets.json \
    --datasetId=3049512673186936334 \
    --references=chr17:41196311:41277499 \
    --hasNonVariantSegments \
    --output=gs://YOUR-BUCKET/output/platinum-genomes-brca1-ibs.tsv

Note that there are several IBS calculators from which to choose. Use the ``--callSimilarityCalculatorFactory`` to switch between them.

Also notice use of the ``--hasNonVariantSegments`` parameter when running this pipeline on the :doc:`/use_cases/discover_public_data/platinum_genomes` dataset.

 * For data with non-variant segments (such as Complete Genomics data or data in Genome VCF (gVCF) format), specify this flag so that the pipeline correctly takes into account non-variant segment records that overlap variants within the dataset.
 * The source :doc:`/use_cases/discover_public_data/platinum_genomes` data imported into `Google Genomics`_ was in gVCF format.

To run the pipeline on a different dataset:

 * change the variant set id for the ``--datasetId`` id parameter.
 * Also, remove the ``--nonVariantSegments`` parameter if it is not applicable.

The above command line runs the pipeline over a small portion of the genome, only taking a few minutes.  If modified to run over a larger portion of the genome or the entire genome, it may take a few hours depending upon how many machines are configured to run concurrently via ``--numWorkers``.  To run this pipeline over a large portion of the genome:

  #. add ``--runner=DataflowPipelineRunner`` to run the pipeline on Google Cloud instead of locally
  #. add more references

    * Use a comma-separated list to run over multiple disjoint regions.  For example to run over `BRCA1`_ and `BRCA2`_ ``--references=chr13:32889610:32973808,chr17:41196311:41277499``
    * Use ``--allReferences`` instead of ``--references=chr17:41196311:41277499`` to run over the entire genome.

Gather the results into a single file
-------------------------------------

.. code-block:: shell

  gsutil cat gs://YOUR-BUCKET/output/platinum-genomes-brca1-ibs.tsv* \
    | sort > platinum-genomes-brca1-ibs.tsv

Additional details
------------------

.. include:: /includes/dataflow_details.rst

