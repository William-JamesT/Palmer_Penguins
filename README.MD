William-james Tafok
Théo Gobé


1) Combien de manchots sont présents dans le jeu de données ?

J'importe le fichier en mettant le chemin dans une variable

fichier = "/home/theo/sae_s204/penguins_size.csv"

ensuite, j'utilise cette commande trouvé dans la doc de scilab pour importer les données en string

data = csvRead(fichier, ",", ".", "string");

Pour finir, on sépare les variables quantitatives des vbariables qualitative dans les variables 
associé au titre des colonnes. On commence a partir de la 2eme ligne pour enlever les titre qui ne sont
pas utiles dans ce cas puisque les noms des variable les remplaces
On Remplace NA par NaN pour certaine variable car on ne peut pas tranformer sinon 

qualitative :

especes = data(2:$, 1);
sexe    = data(2:$, 7);

quantitative : 

longueur_bec = evstr(data(2:$, 3));
profondeur_bec = evstr(data(2:$, 4));
longueur_nageoires = evstr(data(2:$, 5));
masse = evstr(data(2:$, 6));

Pour répondre a la question, on regarde donc le nombre de ligne de la variable espece par exemple 
ce qui va données le nombre total de manchot en tout. La fonction size permet de calculer ca

NbrManchots = size(especes, 1);
NbrManchots
 NbrManchots  = 

   344.

Il y a donc 344 manchots en tout

2) Quelle est la répartition des individus par espèce ?

On trie les différentes espece qui sont présente

especes_uniques = unique(especes);

Ensuite, on les répartie dans leur effectif

effAdelie = sum(especes == especes_uniques(1));
effChinstrap = sum(especes == especes_uniques(2));
effGentoo = sum(especes == especes_uniques(3));

Pour tracer le graphique de bar, on fait la matrice 3x1 des sommes des especes

effectif(1) = effAdelie
 effectif  = 

   152.

-->effectif(2) = effChinstrap
 effectif  = 

   152.
   68.

-->effectif(3) = effGentoo
 effectif  = 

   152.
   68.
   124.

bar(effectif);

![Graphe de bar des effectifs des différentes especes](/home/theo/sae_s204/images/grapheEx2.png)

 

3)Quelle est la répartition des manchots selon leur sexe ?

Il y a une érreur dans la catégorie sexe ou il y a un point. Je ne sais pas ce que cela signifie
donc j'ai arbitrairement décider de remplacer cette valeur par NA.

sexe(sexe == ".") = "NA";

On trie les sexes :

sexes_uniques = unique(sexe);
effectifs_sexe=[];

On compte dans une variable les différent nombre d'effectif

-->for i = 1:size(sexes_uniques, "*")
-->    effectifs_sexe(i) = sum(sexe == sexes_uniques(i));
-->end

-->effectifs_sexe
 effectifs_sexe  = 

   165.
   168.
   11.

bar(effectifs_sexe);

![Graphe de bar des effectifs des différents sexes](/home/theo/sae_s204/images/grapheEx3.png)

4)Calculer la masse moyenne des manchots.

Pour la masse moyenne, on prend la variable sans les valeur pas défini

masse_clean = masse(~isnan(masse));

moyenne_masse = mean(masse_clean);

moyenne_masse
 moyenne_masse  = 

   4201.7544




5)

On ne trie pas les donnée par ordre croissant car d'apres la documentation de scilab la fonction mediane
le fait par défaut

mediane_masse = median(masse_clean);

mediane_masse
 mediane_masse  = 

   4050.

ecart_type_masse = stdev(masse_clean);

ecart_type_masse
 ecart_type_masse  = 

   801.95454


6)Comparer la masse moyenne des trois espèces.

On calcule la masse moyenne pour les 3 especes ainsi que l'ecart type pour la question 7

idx1 = (especes == especes_uniques(1)) & ~isnan(masse);
moy_sp1 = mean(masse(idx1));
std_sp1 = stdev(masse(idx1));


idx2 = (especes == especes_uniques(2)) & ~isnan(masse);
moy_sp2 = mean(masse(idx2));
std_sp2 = stdev(masse(idx2));

idx3 = (especes == especes_uniques(3)) & ~isnan(masse);
moy_sp3 = mean(masse(idx3));
std_sp3 = stdev(masse(idx3));

masses_moyennes    = [moy_sp1; moy_sp2; moy_sp3];
variabilites_masse = [std_sp1; std_sp2; std_sp3];

>masses_moyennes
 masses_moyennes  = 

   3700.6623
   3733.0882
   5076.0163



7)Quelle espèce présente la plus forte variabilité de masse ?

 variabilites_masse  = 

   458.56613
   384.33508
   504.11624

C'est donc l'espece Gentoo qui possede la plus forte variabilité soit le plus grand écart de poid


8)Déterminer les quartiles Q1, Q2 et Q3 de la masse corporelle.

Pour determiner les quartiles, on doit trié les tableau par ordre croissant

masse_triee = gsort(masse_clean, 'g', 'i');
N_clean = length(masse_clean);

On utilise la fonction ceil qui arrondie au supérieur

Q1 = masse_triee(ceil(N_clean * 0.25));
Q2 = median(masse_clean); Q2 est évidemment la médianne donc autant utiliser la fonction médianne
Q3 = masse_triee(ceil(N_clean * 0.75));


 Q1  = 

   3550.
 Q2  = 

   4050.
 Q3  = 

   4750.



9)Calculer l'écart interquartile.


Pour calculer l'écart interquartile, Il suffit juste de faire une différence entre les 2 quartile intéressa>
c'est a dire Q1 et Q3 car Q2 est juste la médiane

ecart_iqr = Q3 - Q1;

 ecart_iqr  = 

   1200.


10) Construire une boîte à moustaches de la masse corporelle.

![Boite a moustache globale de la masse](/home/theo/sae_s204/images/grapheEx10.png)

11) Comparer les boîtes à moustaches des masses selon l'espèce.

![Comparaison de la répartition de la masse selon l'espèce](/home/theo/sae_s204/images/grapheEx11.png)

Question 12

On utilise hisplot

![Histogramme de la masse corporelle](/home/theo/sae_s204/images/grapheEx12.png)

Question 13

calcule des bornes :

bornes = linspace(min(masse_clean), max(masse_clean), n_bins + 1);

On utilise dsearch qui permet de ranger les manchots dans les tranche de poids que on a defini

[ind, counts] = dsearch(masse_clean, bornes);

Pour trouver la classe modale, on cherche donc le maximum

[max_c, id_mod] = max(counts);

Classe modale = [3420 ; 3780]

Pour les fréquence, on divise juste la proportion de la borne par la proportion total :

for k = 1:n_bins
-->    freq(k) = counts(k) / size(masse_clean, "*");
-->end

-->freq
 freq  = 

   0.0438596
   0.1257310
   0.2076023
   0.1549708
   0.1315789
   0.1111111
   0.0818713
   0.0789474
   0.0467836
   0.0175439
Avec disp, on peut obtenir un meilleur affichage :

 "Classe [2700 ; 3060] -> Freq : 0.0438596"

  "Classe [3060 ; 3420] -> Freq : 0.125731"

  "Classe [3420 ; 3780] -> Freq : 0.2076023"

  "Classe [3780 ; 4140] -> Freq : 0.1549708"

  "Classe [4140 ; 4500] -> Freq : 0.1315789"

  "Classe [4500 ; 4860] -> Freq : 0.1111111"

  "Classe [4860 ; 5220] -> Freq : 0.0818713"

  "Classe [5220 ; 5580] -> Freq : 0.0789474"

  "Classe [5580 ; 5940] -> Freq : 0.0467836"

  "Classe [5940 ; 6300] -> Freq : 0.0175439"


Question 14

![Histogramme de la longueur des nageoires](/home/theo/sae_s204/images/grapheEx14.png)


Question 15

On place en abcisse la longueur des nageoire et en ordonnée la masse.

![Relation entre Nageoires et Masse](/home/theo/sae_s204/images/grapheEx15.png)

On remarque que il y a une corrélation positive forte et linéaire. On peut en déduire que le coéfficient
de corrélation est très proche de 1

Question 16

Pour calculer le coefficient, on peut utiliser la fonction scilab correl

r_nag_masse = correl(X_nag(:)', Y_masse(:)');

r_nag_masse
 r_nag_masse  = 

   0.8712018

On remarque que il est proche de 1 d'ou le nuage de point précédent

Question 17

On retire les valeur non affecter et on fait la correlation

ok_bec = ~isnan(longueur_bec) & ~isnan(profondeur_bec);
r_becs = correl(longueur_bec(ok_bec)', profondeur_bec(ok_bec)');

r_becs
 r_becs  = 

  -0.2350529



Question 18

Pour la droite de régression on fait comme dans les tp scilab vu en cours avec reglin:

[a, b, sig] = reglin(X_nag(:)', Y_masse(:)');

a  = 

   49.685566

 b  = 

  -5780.8314

donc on a une équation y = 49.685566 * nageoire - 5780.8314

Question 20

Pour le coefficient de détermination, c'est la correlation des nageoires et des masses au carré

R2 = r_nag_masse^2;

-->R2
 R2  = 

   0.7589925

Question 21

On remplace nageoire dans l'équation précédente par la longeur ou l'on veut déterminer la masse : 

pred_210 = a * 210 + b;

-->pred_210
 pred_210  = 

   4653.1376

Donc un manchot ayant une nageoire de 210 mm aura une masse qui tournera autour de 4653.1376. Selon le 
coefficient de determination, cette aproximation est assez proche


Question 22

On utilise une boucle :

for i = 1:size(especes_uniques, "*")
    idx_sp = (especes == especes_uniques(i)) & ok_biv;
    if sum(idx_sp) > 1 then
        X_sp = longueur_nageoires(idx_sp)';
        Y_sp = masse(idx_sp)';
        [a_sp, b_sp, s_sp] = reglin(X_sp, Y_sp);
        r_sp = correl(X_sp, Y_sp);
        disp(especes_uniques(i) + " -> Pente (a) = " + string(a_sp) + " | Corrélation (r) = " + string(r_sp));
    end
end


On obtient donc les pentes et le coef de correlation pour les 3 especes :

  "Adelie -> Pente (a) = 32.83169 | Corrélation (r) = 0.4682017"

  "Chinstrap -> Pente (a) = 34.573394 | Corrélation (r) = 0.6415594"

  "Gentoo -> Pente (a) = 54.622502 | Corrélation (r) = 0.7026665"


Question 23

On fait pareil que les questions précédentes mais pour les sexes féminin et masculin

idx_sx1 = (sexe == sexes_uniques(1)) & ~isnan(masse);
moy_females = mean(masse(idx_sx1));
med_females = median(masse(idx_sx1));
std_females = stdev(masse(idx_sx1));

idx_sx2 = (sexe == sexes_uniques(2)) & ~isnan(masse);
moy_males = mean(masse(idx_sx2));
med_males = median(masse(idx_sx2));
std_males = stdev(masse(idx_sx2));

idx_sx3 = (sexe == sexes_uniques(3)) & ~isnan(masse);
moy_NA = mean(masse(idx_sx3));
med_NA = median(masse(idx_sx3));
std_NA = stdev(masse(idx_sx3));

On fait une matrice qui concentre tout les résultat par soucis de simpliciter

stats_sexe = [moy_females, med_females, std_females;
              moy_males,   med_males,   std_males;
              moy_NA,      med_NA,      std_NA];

-->stats_sexe
 stats_sexe  = 

   3862.2727   3650.   666.17205
   4545.6845   4300.   787.62888
   4005.5556   4100.   679.35836

On remarque donc que les males sont en moyenne plus lourds que les femelles 


Question 24

On enleve les NaN 

ok_becL_masse = ~isnan(longueur_bec) & ~isnan(masse);
ok_becP_masse = ~isnan(profondeur_bec) & ~isnan(masse);
ok_nag_masse  = ~isnan(longueur_nageoires) & ~isnan(masse);

On calcule les differentes correlation linéaire

r_bec_longueur   = correl(longueur_bec(ok_becL_masse)', masse(ok_becL_masse)');
r_bec_profondeur = correl(profondeur_bec(ok_becP_masse)', masse(ok_becP_masse)');
r_nageoires      = correl(longueur_nageoires(ok_nag_masse)', masse(ok_nag_masse)');

Pour finir, on stock les valeurs absolue des différentes corrélation :

corr_globales_masse = [abs(r_bec_longueur); abs(r_bec_profondeur); abs(r_nageoires)];

-->corr_globales_masse
 corr_globales_masse  = 

   0.5951098
   0.4719156
   0.8712018

On peut voir que le facteur le plus prédicteur du poid est la longueur des nageoire largement avec un 
coefficient a 0.87
Ensuite, c'est la longueur du bec et enfin en dernier la profondeur du bec
