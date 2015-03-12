Q1) Join : Algo naïf

	Pour chaque enregistrement d'une première table on associe, selon un critère, tous les enregistrements d'une seconde table.

	Table t1, t2
	Critère c
	Pour chaque enregistrement e1 de t1
		Pour chaque enregistrement e2 de t2
			Si c de e1,e2 vrai alors
				output e1+e2


Q2) Nested Join

		1- batch ?

			Traitement par lots (pages).

		2- materialiser ?



		3- next 
			
			3.1- Quand retourne-t-elle ?

				Lorsque la page (buffer) de sortie est remplie.

			3.2- Quand est-elle appelée ?

				On appelle next() sur le buffer gauche quand on a testé toutes les correspondances avec les données du buffer droit.
				On appelle next() sur le buffer droit pour tester chaque enregistrement du buffer droit.
		
		4- open
			
			Quand est-elle appelée ?

				Pour l'initialisation (dans le main)
		
		5- pseudo-code
			
			Est-il le cours ?