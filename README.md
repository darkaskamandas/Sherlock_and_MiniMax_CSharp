# Sherlock_and_MiniMax_CSharp
Practice Algorithms Greedy Sherlock and MiniMax C#




using System;
using System.Linq;
using System.Text;
using System.IO;
using System.Diagnostics;
using System.Globalization;
using System.Collections.Generic;
using System.Threading;
using kp.Algo;

namespace CodeForces
{
	internal class Solution
	{
		const int StackSize = 20 * 1024 * 1024;

		private void Solve()
		{
			int n = NextInt();
			int[] a = NextIntArray( n );
			int p = NextInt(), q = NextInt();
			Array.Sort( a );

			var tries = new List<int>();
			for ( int i = 0; i < n - 1; ++i )
			{
				if ( ( a[i] + a[i + 1] ) / 2 >= p && ( a[i] + a[i + 1] ) / 2 <= q )
					tries.Add( ( a[i] + a[i + 1] ) / 2 );
				if ( ( a[i] + a[i + 1] ) / 2 + 1 >= p && ( a[i] + a[i + 1] ) / 2 + 1 <= q )
					tries.Add( ( a[i] + a[i + 1] ) / 2 + 1 );
			}
			tries.Add( p );
			tries.Add( q );

			tries.Sort();
			int res = int.MinValue, ans = 0;
			for ( int i = 0; i < tries.Count; ++i )
			{
				int d = int.MaxValue;
				foreach ( var c in a )
				{
					d = Math.Min( d, Math.Abs( c - tries[i] ) );
				}
				if ( d > res )
				{
					res = d;
					ans = tries[i];
				}
			}
			Out.WriteLine( ans );
		}

		#region Local wireup

		public int[] NextIntArray( int size )
		{
			var res = new int[size];
			for ( int i = 0; i < size; ++i ) res[i] = NextInt();
			return res;
		}

		public long[] NextLongArray( int size )
		{
			var res = new long[size];
			for ( int i = 0; i < size; ++i ) res[i] = NextLong();
			return res;
		}

		public double[] NextDoubleArray( int size )
		{
			var res = new double[size];
			for ( int i = 0; i < size; ++i ) res[i] = NextDouble();
			return res;
		}

		public int NextInt()
		{
			return _in.NextInt();
		}

		public long NextLong()
		{
			return _in.NextLong();
		}

		public string NextLine()
		{
			return _in.NextLine();
		}

		public double NextDouble()
		{
			return _in.NextDouble();
		}

		readonly Scanner _in = new Scanner();
		static readonly TextWriter Out = Console.Out;

		void Start()
		{
#if KP_HOME
			var timer = new Stopwatch();
			timer.Start();
#endif
			var t = new Thread( Solve, StackSize );
			t.Start();
			t.Join();
#if KP_HOME
			timer.Stop();
			Console.WriteLine( string.Format( CultureInfo.InvariantCulture, "Done in {0} seconds.\nPress <Enter> to exit.", timer.ElapsedMilliseconds / 1000.0 ) );
			Console.ReadLine();
#endif
		}

		static void Main()
		{
			new Solution().Start();
		}

		class Scanner : IDisposable
		{
			#region Fields

			readonly TextReader _reader;
			readonly int _bufferSize;
			readonly bool _closeReader;
			readonly char[] _buffer;
			int _length, _pos;

			#endregion

			#region .ctors

			public Scanner( TextReader reader, int bufferSize, bool closeReader )
			{
				_reader = reader;
				_bufferSize = bufferSize;
				_closeReader = closeReader;
				_buffer = new char[_bufferSize];
				FillBuffer( false );
			}

			public Scanner( TextReader reader, bool closeReader ) : this( reader, 1 << 16, closeReader ) { }

			public Scanner( string fileName ) : this( new StreamReader( fileName, Encoding.Default ), true ) { }

#if !KP_HOME
			public Scanner() : this( Console.In, false ) { }
#else
			public Scanner() : this( "input.txt" ) { }
#endif

			#endregion

			#region IDisposable Members

			public void Dispose()
			{
				if ( _closeReader )
				{
					_reader.Close();
				}
			}

			#endregion

			#region Properties

			public bool Eof
			{
				get
				{
					if ( _pos < _length ) return false;
					FillBuffer( false );
					return _pos >= _length;
				}
			}

			#endregion

			#region Methods

			private char NextChar()
			{
				if ( _pos < _length ) return _buffer[_pos++];
				FillBuffer( true );
				return _buffer[_pos++];
			}

			private char PeekNextChar()
			{
				if ( _pos < _length ) return _buffer[_pos];
				FillBuffer( true );
				return _buffer[_pos];
			}

			private void FillBuffer( bool throwOnEof )
			{
				_length = _reader.Read( _buffer, 0, _bufferSize );
				if ( throwOnEof && Eof )
				{
					throw new IOException( "Can't read beyond the end of file" );
				}
				_pos = 0;
			}

			public int NextInt()
			{
				var neg = false;
				int res = 0;
				SkipWhitespaces();
				if ( !Eof && PeekNextChar() == '-' )
				{
					neg = true;
					_pos++;
				}
				while ( !Eof && !IsWhitespace( PeekNextChar() ) )
				{
					var c = NextChar();
					if ( c < '0' || c > '9' ) throw new ArgumentException( "Illegal character" );
					res = 10 * res + c - '0';
				}
				return neg ? -res : res;
			}

			public long NextLong()
			{
				var neg = false;
				long res = 0;
				SkipWhitespaces();
				if ( !Eof && PeekNextChar() == '-' )
				{
					neg = true;
					_pos++;
				}
				while ( !Eof && !IsWhitespace( PeekNextChar() ) )
				{
					var c = NextChar();
					if ( c < '0' || c > '9' ) throw new ArgumentException( "Illegal character" );
					res = 10 * res + c - '0';
				}
				return neg ? -res : res;
			}

			public string NextLine()
			{
				SkipUntilNextLine();
				if ( Eof ) return "";
				var builder = new StringBuilder();
				while ( !Eof && !IsEndOfLine( PeekNextChar() ) )
				{
					builder.Append( NextChar() );
				}
				return builder.ToString();
			}

			public double NextDouble()
			{
				SkipWhitespaces();
				var builder = new StringBuilder();
				while ( !Eof && !IsWhitespace( PeekNextChar() ) )
				{
					builder.Append( NextChar() );
				}
				return double.Parse( builder.ToString(), CultureInfo.InvariantCulture );
			}

			private void SkipWhitespaces()
			{
				while ( !Eof && IsWhitespace( PeekNextChar() ) )
				{
					++_pos;
				}
			}

			private void SkipUntilNextLine()
			{
				while ( !Eof && IsEndOfLine( PeekNextChar() ) )
				{
					++_pos;
				}
			}

			private static bool IsWhitespace( char c )
			{
				return c == ' ' || c == '\t' || c == '\n' || c == '\r';
			}

			private static bool IsEndOfLine( char c )
			{
				return c == '\n' || c == '\r';
			}

			#endregion
		}

		#endregion
	}
}

namespace kp.Algo { }
