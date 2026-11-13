# Local Keshif distribution

This is the application's existing vendored Keshif+ snapshot, with targeted
Portic corrections in `keshif.js`. It is not a migration to a new upstream version.
The original snapshot is retained in repository history at `0addf25`.

The application now consistently loads Keshif JS, CSS, PapaParse, assets and
mapping dependencies from this directory. The old `static/lib/keshif-dist`
copy is retained for compatibility but is no longer referenced by the templates.

## Corrections

- Optional WebFont loading must not prevent layout/resize initialization.
- Hidden dashboards do not recompute their layout, and layout changes cannot
  recursively re-enter themselves while panels collapse or expand.
- Spatial filters retain their geographic attribute and tolerate missing locations.
- Point cluster preparation excludes absent geometries without removing records
  from other views; empty geographic data uses valid default map bounds.
- Reloading a geographic role resets its missing-value aggregate instead of
  accumulating duplicate missing-position records.
- Cluster membership uses the actual number of members, rather than truncating
  calculations and filtering at 1000 records.
- Map-only size/color measures are not applied to list/scatter views. Constant
  pin size is also supported. Returning to the map can restore its cluster measure.
- A host application may set `confirmLargeRecordView: false` to avoid the old
  blocking view-change modal. The default for other integrations is preserved.

## Rebuild and verification

```bash
npm ci
npm run build:keshif
npm test
npm run test:browser
```

Only development requires Node (20+). The generated bundle and source map are
committed deployment assets; Flask does not run npm. Terser is pinned in
`package-lock.json`. Edit the readable module, not the minified file. The new
minified source map points to this module; the old upstream TypeScript source
map is no longer referenced by the patched readable module.

Browser tests use real project CSVs and locally provided versions of jQuery and
Bootstrap. They replace external CARTO tiles with a test image; they do not prove
availability of the real tile service. Upstream: https://github.com/keshifme/keshif-plus
Preserve existing upstream and bundled dependency notices when redistributing.

## Lisibilité des vues centrales et performances

- Option `minRecordViewHeight` : réserver une hauteur utile au widget central
  (460 px dans Portic), au lieu des 200 px fixes de la bibliothèque.
- Recalculer les échelles du scatterplot quand sa taille change.
- Mesurer les étiquettes par lots avant de modifier leurs dimensions, pour éviter
  un recalcul de mise en page par trajet ; comparer les chevauchements seulement
  avec les étiquettes déjà retenues.

Les corrections CSS propres à Portic restent dans `static/css/styles.css`.
En particulier, la règle globale `.header` du thème s'appliquait aux libellés
Size/Color de Keshif : la surcharge locale rétablit leur espacement.

- Options `scatterLabels: false` et `compactPointDOM: true` : éviter les étiquettes
  permanentes dans les nuages denses et créer les glyphes de comparaison à la
  demande. Les points, les infobulles et les comparaisons restent disponibles.
- Dans Portic, différer l'affichage initial du scatterplot jusqu'au premier
  calcul de dimensions du dashboard ; réserver les titres des facettes centrales
  quand la place verticale est limitée.

## Retours de l'étape 1

- Inférence des types : une comparaison stricte à la chaîne vide permet de
  reconnaître les nombres zéro et booléens faux. Les deux colonnes d'incertitude
  G5 contiennent uniquement zéro ; leur avertissement n'établissait pas un gel.
- Liste : le clic sur un lien dans le contenu conserve la navigation du lien
  et ne déclenche pas la fiche du trajet.
- Le fournisseur des cartes est défini dans `static/js/map-config.js`, commun
  aux cartes centrales et catégorielles. Voir `docs/corrections-etape1.md` pour
  les limites de validation du fournisseur réel et du blocage G5.

## Consolidation des redimensionnements (étape 2)

- Pendant `Browser.updateLayout`, différer le rafraîchissement de la vue centrale
  jusqu'à ce que largeur et hauteur soient toutes deux appliquées. Un nuage de
  29 879 points n'est ainsi pas redessiné une fois par dimension dans ce même
  calcul de layout. Les appels isolés aux setters gardent leur comportement.
- L'adaptation au conteneur, au défilement sur petits écrans et aux panneaux
  latéraux repliés reste dans le code applicatif (`observeDashboardSize`), sans
  modifier le comportement natif de repli Keshif.
- Recette de non-régression : `npm run test:lifecycle`, sur les trois CSV réels.
