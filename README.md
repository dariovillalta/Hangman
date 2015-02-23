# Hangman
#include <iostream>
#include <string>
#include <fstream>
#include <cstdlib> //random y exit
#include <vector>
#include <cstring>
using namespace std;

void imprimirHangman(int);

int main(int argc, char const *argv[])
{
	string continuar;

	do{
		int menu;
		do{
			cout << endl;
			cout << endl;
			cout << "\t _   _                                         " << endl;
			cout << "\t| | | | __ _ _ __   __ _ _ __ ___   __ _ _ __  " << endl;
			cout << "\t| |_| |/ _` | '_ \\ / _` | '_ ` _ \\ / _` | '_ \\ " << endl;
			cout << "\t|  _  | (_| | | | | (_| | | | | | | (_| | | | |" << endl;
			cout << "\t|_| |_|\\__,_|_| |_|\\__, |_| |_| |_|\\__,_|_| |_|" << endl;
			cout << "\t                   |___/                       " << endl;
			cout << endl;
			cout << "1) Jugar\n2) Jugar vs Humano\n3) Agregar Palabra\n4) Salir\nSeleccione una opcion: ";
			cin >> menu;
			cout << endl;
			switch(menu){
				case 1:{
					ifstream in;
					cout << "Seleccione un genero:\n1) Nombre de Futbolistas\n2) Nombre de Equipos de Futbol\n3) Nombre de Artistas\n4) Ciudades Famosas\n";
					cout << "5) Programadores Famosos\nIngrese su respuesta: ";
					int genero;
					cin >> genero;
					cout << endl;
					switch(genero){
						case 1:{
							in.open("Futbolistas.txt");
						}
							break;
						case 2:{
							in.open("Futbol.txt");
						}
							break;
						case 3:{
							in.open("Artistas.txt");
						}
							break;
						case 4:{
							in.open("Ciudades.txt");
						}
							break;
						case 5:{
							in.open("Programadores.txt");
						}
							break;
						default:{
							cout << "Seleccione una opcion valida.\nIngrese de nuevo: ";
							cin >> genero;
							cout << endl;
						}
					}
					
					//Prueba si existe
					if(in.fail()){
						cerr << "Error abriendo el archivo." << endl;
						exit(1);
					}

					//llenando arreglo del diccionario & asignacion de palabra secreta
					vector<string> diccionario;
					string linea;
					while(!in.eof()){
						in >> linea;
						diccionario.push_back(linea);
					}

					srand(time(0));
					int random = rand() % diccionario.size();
					char* palabraSecreta = new char[diccionario.size() + 1];//[diccionario[random]+1];
					strcpy( palabraSecreta, diccionario[random].c_str() ); //aquiiiiiiiiiiiiiiiiiiiiiiiī
					char imprimir[strlen(palabraSecreta) + 1];
					strcpy( imprimir, palabraSecreta);

					//cout << palabraSecreta << endl << endl;
					for (int i = 0; i < strlen(palabraSecreta); ++i){
						imprimir[i] = ' ';
					}

					bool gano = false, perdio = false;
					int malas = 0, buenas, acertadas = 0, erradas = 0;
					char* aciertos =  new char[strlen(palabraSecreta) + 3];
					char* errores = new char[10];
					string letra;
					do{
						buenas = 0;
						imprimirHangman(malas);
						cout << endl;

						cout << "\t";
						for (int i = 0; i < strlen(imprimir); ++i){
							if(imprimir[i] == ' ')
								cout << "*";
							else{
								buenas++;
								cout << imprimir[i];
							}
						}
						cout << endl << endl;
						cout << "Letras Acertadas: ";
						for (int i = 0; i < strlen(aciertos); ++i){
							cout << aciertos[i] << " ";
						}
						cout << endl << "Letras Erradas: ";
						for (int i = 0; i < strlen(errores); ++i){
							cout << errores[i] << " ";
						}
						cout << endl << "Ingrese una letra o ingrese 00 para adivinar la palabra: " ;
						cin >> letra;
						bool intento = false;
						if(letra == "00"){
							intento = true;
							buenas = 0;
							cout << "Ingrese su respuesta: ";
							string respuesta;
							cin >> respuesta;
							cout << endl;
							if (respuesta.length() == strlen(palabraSecreta)){
								for (int i = 0; i < strlen(palabraSecreta); ++i){
									char a = respuesta[i], b = palabraSecreta[i];
									a = tolower(a);
									b = tolower(b);
									if(a == b){
										imprimir[i] = palabraSecreta[i];
										aciertos[strlen(aciertos)] =  respuesta[i];
										buenas++;
									}
								}
								buenas--;
							} else{
								perdio = true;
							}
						}
						if(intento == false){
							while(letra.length()>1){
								cerr << "Ingrese solo una letra: ";
								cin >> letra;
								cout << endl;
							}
							for (int i = 0; i < strlen(aciertos); ++i){
								while(letra[0] == aciertos[i]){
									cerr << "Ya habia ingresado la letra.\nIngrese de nuevo: ";
									cin >> letra;
									cout << endl;
								}
							}

							bool encontro =  false;
							for (int i = 0; i < strlen(palabraSecreta); ++i){
								char a = letra[0], b = palabraSecreta[i];
								a = tolower(a);
								b = tolower(b);
								if(a == b){
									imprimir[i] = palabraSecreta[i];
									aciertos[strlen(aciertos)] =  letra[0];
									encontro = true;
									acertadas++;
								}
							}
						
							if (encontro == false){
								errores[strlen(errores)] = letra[0];
								malas++;
								erradas++;
							}
						} else
							perdio = true;
						if(malas == 8){
							perdio = true;
							imprimirHangman(malas);
						}
						//cout << strlen(palabraSecreta)<< "///" << buenas<<endl;
						if(buenas == strlen(palabraSecreta) - 1)
							gano = true;
					} while(gano == false && perdio == false);
					if(perdio == true){
						cout << "Ha perdido.\nLa palabra secreta era: " << palabraSecreta << endl;
						cout << "Total de aciertos: " << acertadas << endl;
						cout << "Letras Acertadas: ";
						for (int i = 0; i < strlen(aciertos); ++i){
							cout << aciertos[i] << " ";
						}
						cout << "Total de erradas: " << erradas << endl;
						cout << endl << "Letras Erradas: ";
						for (int i = 0; i < strlen(errores); ++i){
							cout << errores[i] << " ";
						}
					}
					if(gano == true){
						cout << "Ha ganado!\nLa palabra secreta: " << palabraSecreta << endl;
						cout << "Total de aciertos: " << acertadas << endl;
						cout << "Letras Acertadas: ";
						for (int i = 0; i < strlen(aciertos); ++i){
							cout << aciertos[i] << " ";
						}
						cout << "Total de erradas: " << erradas << endl;
						cout << endl << "Letras Erradas: ";
						for (int i = 0; i < strlen(errores); ++i){
							cout << errores[i] << " ";
						}
					}
				}
					break;
				case 2:{
					cout << "Ingrese la palabra secreta: ";
					string diccionario;
					cin >> diccionario;
					char* palabraSecreta = new char[20];//[diccionario[random]+1];
					strcpy( palabraSecreta, diccionario.c_str() ); //aquiiiiiiiiiiiiiiiiiiiiiiiī
					char imprimir[strlen(palabraSecreta) + 1];
					strcpy( imprimir, palabraSecreta);

					for (int i = 0; i < strlen(palabraSecreta); ++i){
						imprimir[i] = ' ';
					}

					bool gano = false, perdio = false;
					int malas = 0, buenas, acertadas = 0, erradas = 0;
					char* aciertos =  new char[strlen(palabraSecreta) + 1];
					char* errores = new char[9];
					char letra;
					do{
						buenas = 0;
						imprimirHangman(malas);
						cout << endl;

						cout << "\t";
						for (int i = 0; i < strlen(imprimir); ++i){
							if(imprimir[i] == ' ')
								cout << "*";
							else{
								cout << imprimir[i];
								buenas++;
							}
						}
						cout << endl << endl;

						cout << "Letras Acertadas: ";
						for (int i = 0; i < strlen(aciertos); ++i){
							cout << aciertos[i] << " ";
						}
						
						cout << endl << "Letras Erradas: ";
						for (int i = 0; i < strlen(errores); ++i){
							cout << errores[i] << " ";
						}
						cout << endl << "Ingrese una letra: ";
						cin >> letra;
						for (int i = 0; i < strlen(aciertos); ++i){
							while(letra == aciertos[i]){
								cout << "Ya habia ingresado la letra.\nIngrese de nuevo: ";
								cin >> letra;
								cout << endl;
							}
						}

						bool encontro =  false;
						for (int i = 0; i < strlen(palabraSecreta); ++i){
							char a = letra, b = palabraSecreta[i];
							a = tolower(a);
							b = tolower(b);
							if(a == b){
								imprimir[i] = palabraSecreta[i];
								aciertos[strlen(aciertos)] =  letra;
								encontro = true;
								acertadas++;
							}
						}
						if (encontro == false){
							errores[strlen(errores)] = letra;
							malas++;
							erradas++;
						}

						if(malas == 8){
							perdio = true;
							imprimirHangman(malas);
						}
						if(buenas == strlen(palabraSecreta) - 1)
							gano = true;
					} while(gano == false && perdio == false);
					if(perdio == true){
						cout << "Ha perdido.\nLa palabra secreta era: " << palabraSecreta << endl;
						cout << "Total de aciertos: " << acertadas << endl;
						cout << "Letras Acertadas: ";
						for (int i = 0; i < strlen(aciertos); ++i){
							cout << aciertos[i] << " ";
						}
						cout << "Total de erradas: " << erradas << endl;
						cout << endl << "Letras Erradas: ";
						for (int i = 0; i < strlen(errores); ++i){
							cout << errores[i] << " ";
						}
					}
					if(gano == true){
						cout << "Ha ganado!\nLa palabra secreta: " << palabraSecreta << endl;
						cout << "Total de aciertos: " << acertadas << endl;
						cout << "Letras Acertadas: ";
						for (int i = 0; i < strlen(aciertos); ++i){
							cout << aciertos[i] << " ";
						}
						cout << "Total de erradas: " << erradas << endl;
						cout << endl << "Letras Erradas: ";
						for (int i = 0; i < strlen(errores); ++i){
							cout << errores[i] << " ";
						}
					}
				}
					break;
				case 3:{
						ifstream in;
						cout << "Seleccione un genero:\n1) Nombre de Futbolistas\n2) Nombre de Equipos de Futbol\n3) Nombre de Artistas\n4) Ciudades Famosas\n";
						cout << "5) Programadores Famosos\nIngrese su respuesta: ";
						int genero;
						cin >> genero;
						cout << endl;
						switch(genero){
							case 1:{
								in.open("Futbolistas.txt");
							}
								break;
							case 2:{
								in.open("Futbol.txt");
							}
								break;
							case 3:{
								in.open("Artistas.txt");
							}
								break;
							case 4:{
								in.open("Ciudades.txt");
							}
								break;
							case 5:{
								in.open("Programadores.txt");
							}
								break;
							default:{
								cout << "Seleccione una opcion valida.\nIngrese de nuevo: ";
								cin >> genero;
								cout << endl;
							}
						}
						
						//Prueba si existe
						if(in.fail()){
							cerr << "Error abriendo el archivo." << endl;
							exit(1);
						}

						cout << "Palabras existentes:" << endl;
						string palabraDic, palabraAg;
						int con = 0;
						//Imprime las palabras existentes para el usuario
						while(!in.eof()){
							in >> palabraDic;
							if(con % 2 == 0)
								cout << "\t" << palabraDic;
							else
								cout << "\t" << palabraDic << endl;
							con++;
						}
						cout << endl << "Ingrese la palabra que desea agregar: ";
						cin >> palabraAg;
						cout << endl;
						in.clear();
						in.seekg (0, in.beg);
						vector <string> Diccionario;

						//Guarda las palabras en un vector temporal
						while(!in.eof()){
							in >> palabraDic;
							Diccionario.push_back(palabraDic);
						}
						Diccionario.push_back(palabraAg);
						in.close();
						ofstream out;
						switch(genero){
							case 1:{
								out.open("Futbolistas.txt");
							}
								break;
							case 2:{
								out.open("Futbol.txt");
							}
								break;
							case 3:{
								out.open("Artistas.txt");
							}
								break;
							case 4:{
								out.open("Ciudades.txt");
							}
								break;
							case 5:{
								out.open("Programadores.txt");
							}
								break;
						}
						//out.open("Diccionario.txt"/*, std::fstream::app | std::fstream::out*/);
						for (int i = 0; i < Diccionario.size(); ++i){
							if(i !=Diccionario.size()-1)
								out << Diccionario[i] << endl;
							else
								out << Diccionario[i];
						}
						out.close();

					}
					break;
				case 4:{

				}
					break;
				default:{
					cout << "Seleccione una opcion valida.\nIngrese de nuevo: ";
					cin >> menu;
					cout << endl;
				}
			}
		}while(menu != 4);
		cout << "Desea jugar de nuevo? [si/no]" << endl;
		cin >> continuar;
	} while (continuar == "si" || continuar == "Si" || continuar == "SI" || continuar == "sI");
	return 0;
}

void imprimirHangman(int numeroMalas){
	switch(numeroMalas){
		case 0:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||" << endl << 
					"| |/ /       ||" << endl << 
					"| | /        ||" << endl << 
					"| |/         ||" << endl << 
					"| |          ||" << endl << 
					"| |          ()" << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 1:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 2:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |         /Y. .Y\\  " << endl << 
					"| |          |   | " << endl << 
					"| |          | . | " << endl << 
					"| |          |   | " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 3:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |        //Y. .Y\\  " << endl << 
					"| |       // |   | " << endl << 
					"| |      //  | . | " << endl << 
					"| |     ')   |   | " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |     " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 4:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |        //Y. .Y\\\\  " << endl << 
					"| |       // |   | \\\\" << endl << 
					"| |      //  | . |  \\\\" << endl << 
					"| |     ')   |   |   (`" << endl << 
					"| |    " << endl << 
					"| |    " << endl << 
					"| |    " << endl << 
					"| |    " << endl << 
					"| |    " << endl << 
					"| |    " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 5:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |        //Y. .Y\\\\  " << endl << 
					"| |       // |   | \\\\" << endl << 
					"| |      //  | . |  \\\\" << endl << 
					"| |     ')   |   |   (`" << endl << 
					"| |          ||  " << endl << 
					"| |          ||  " << endl << 
					"| |          ||  " << endl << 
					"| |          ||  " << endl << 
					"| |         " << endl << 
					"| |          " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 6:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |        //Y. .Y\\\\  " << endl << 
					"| |       // |   | \\\\" << endl << 
					"| |      //  | . |  \\\\" << endl << 
					"| |     ')   |   |   (`" << endl << 
					"| |          ||'||     " << endl << 
					"| |          || ||     " << endl << 
					"| |          || ||     " << endl << 
					"| |          || ||     " << endl << 
					"| |           " << endl << 
					"| |           " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 7:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |        //Y. .Y\\\\  " << endl << 
					"| |       // |   | \\\\" << endl << 
					"| |      //  | . |  \\\\" << endl << 
					"| |     ')   |   |   (`" << endl << 
					"| |          ||'||     " << endl << 
					"| |          || ||     " << endl << 
					"| |          || ||     " << endl << 
					"| |          || ||     " << endl << 
					"| |         / |   " << endl << 
					"| |         `-'   " << endl << 
					"| |		" << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
		case 8:{
			cout << " ___________.._______ " << endl << 
					"| .__________))______| " << endl << 
					"| | / /      ||		   " << endl << 
					"| |/ /       ||		   " << endl << 
					"| | /        ||.-''.   " << endl << 
					"| |/         |/  _  \\ " << endl << 
					"| |          ||  `/,|  " << endl << 
					"| |          (\\\\`_.' " << endl << 
					"| |         .-`--'.    " << endl << 
					"| |        //Y. .Y\\\\  " << endl << 
					"| |       // |   | \\\\" << endl << 
					"| |      //  | . |  \\\\" << endl << 
					"| |     ')   |   |   (`" << endl << 
					"| |          ||'||     " << endl << 
					"| |          || ||     " << endl << 
					"| |          || ||     " << endl << 
					"| |          || ||     " << endl << 
					"| |         / | | \\   " << endl << 
					"| |         `-' `-'    " << endl << 
					"| |					   " << endl << 
					"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"|\"\"\"|" << endl << 
					"|\"|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\'\"|\"| " << endl << 
					"| |                   | | " << endl << 
					": :                   : : " << endl << 
					". .                   . . " << endl;
		}
			break;
	}
}
# Hangman
# Hangman
