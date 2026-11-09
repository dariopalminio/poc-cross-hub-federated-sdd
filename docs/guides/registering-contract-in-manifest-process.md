# Registrar contrato compartido en el hub central (sincronización)

El manifiesto de la iniciativa declara shared_contracts: [], pero la constitución Frontend exige una revisión inmutable del contrato para planificar su consumo. 

1. Si el Backend confirma que el contrato OpenAPI está en su ruta canónica, corresponde a la implementación y está disponible en un commit. Anotar el SHA completo de ese commit, algo así:

    owner_repo: https://github.com/dariopalminio/poc-be-federated-sdd
    path: specs/001-health-endpoint/contracts/openapi.json
    revision: 44ff3c4576062262370b4f7798b24e1eddc2abde

2. Actualizar shared_contracts en el manifiesto del Hub con el contrato y ese SHA:

3. Integrar ese cambio en el Hub y anoten el SHA completo del commit del Hub. Son dos revisiones distintas: revision apunta al commit Backend con el contrato; source_revision en origin.yaml apunta al commit Hub con la spec y el manifiesto.

4. Actualizar los origin.yaml afectados para fijar esa nueva revisión del Hub. El Frontend entonces puede derivar su spec contra el manifiesto actualizado y planificar el consumo del contrato fijado.

5. Hasta que ese cambio del Hub esté integrado, mantengan bloqueada la planificación Frontend: no cambien su constitución ni sustituyan shared_contracts: [] por una elección local. El fronten debe actualizar su source_revision en el origin.yaml.

