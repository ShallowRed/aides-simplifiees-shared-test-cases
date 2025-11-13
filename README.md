# Aides Simplifiées - Shared Test Cases

Format JSON commun pour les cas de test partagés entre :
- **Dépôt Node.js/TypeScript** : [aides-simplifiees-app](https://github.com/betagouv/aides-simplifiees-app)
- **Dépôt Python** : [aides-calculatrice-back](https://github.com/betagouv/aides-calculatrice-back)

## Vue d'ensemble

Ce dépôt contient les **cas de test partagés** qui servent de source de vérité unique pour valider les calculs de prestations sociales. Le format JSON permet de :

1. **Source de vérité unique** : Un seul endroit pour définir les cas de test
2. **Tests de non-régression** : Exécutés dans la CI/CD des deux dépôts
3. **Alimentation des personas** : Les personas de l'interface admin sont dérivés de ces test cases
4. **Traçabilité temporelle** : Chaque test case est lié à une période législative (`period`)

## Structure du dépôt

```
.
├── schema.json                          # JSON Schema pour validation
├── README.md                            # Ce fichier README
├── [simulateur_id]/
    ├── test-cases.json                  # Cas de test pour ce simulateur
    └── snapshots/                       # Snapshots par période et version
```

## Utilisation

### Dans le dépôt Node.js (git submodule)

```bash
# Ajouter comme git submodule
cd aides-simplifiees-app
git submodule add https://github.com/betagouv/aides-simplifiees-shared-test-cases.git shared-test-cases
git submodule update --init --recursive

# Charger les test cases
```

```typescript
import testCases from './shared-test-cases/demenagement-logement/test-cases.json'

const testCase = testCases.test_cases[0]
console.log(testCase.name)
console.log(testCase.period)
```

### Dans le dépôt Python (git submodule)

```bash
# Ajouter comme git submodule
cd aides-calculatrice-back
git submodule add https://github.com/betagouv/aides-simplifiees-shared-test-cases.git tests/shared-test-cases
git submodule update --init --recursive

# Charger les test cases
```

```python
import json

with open('tests/shared-test-cases/demenagement-logement/test-cases.json') as f:
    test_cases = json.load(f)

for test_case in test_cases['test_cases']:
    period = test_case['period']
    openfisca_request = test_case['openfisca_request']
```

## Validation

### Valider contre le schéma JSON

```bash
# Avec Python
pip install jsonschema
python -m jsonschema -i demenagement-logement/test-cases.json schema.json

# Avec Node.js (ajv-cli)
npm install -g ajv-cli
ajv validate -s schema.json -d "*/test-cases.json"
```

## Contribution

### Créer un nouveau test case

1. Choisir le simulateur concerné (ou créer un nouveau dossier)
2. Ajouter le test case dans \`test-cases.json\`
3. Valider contre le schéma
4. Renseigner les métadonnées de validation
5. Créer une Pull Request

### Conventions

- **ID** : Format \`{simulateur}-{numero}\` (ex: \`dem-log-001\`)
- **Period** : Format \`YYYY-MM\` (ex: \`2025-01\`)
- **Validation experte** : Obligatoire avant merge

## Versionning

Version actuelle : **1.0.0**

- **MAJOR** : Changements incompatibles dans le format
- **MINOR** : Ajout de nouveaux test cases ou champs optionnels
- **PATCH** : Corrections de données ou documentation

## Licence

MIT License - voir [LICENSE](LICENSE)

## Support

- **Issues** : https://github.com/betagouv/aides-simplifiees-shared-test-cases/issues
- **Documentation complète** : voir README.md
